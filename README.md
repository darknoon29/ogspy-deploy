# ogspy-deploy

Repository containing the GitHub Actions workflow that deploys [OGSpy](https://github.com/OGSteam/ogspy) to Infomaniak shared hosting.

Each alliance gets its own isolated deployment in a dedicated folder with a unique database table prefix.

A deployment is triggered by opening an issue with a specific form, and **requires manual approval from the owner** before any files are transferred.

> **Repository visibility:** This repository must be **public** so that any GitHub user can open a deployment-request issue (ticket). The deployment is still fully protected because:
> 1. Only users with at least **Write** access can actually trigger the workflow (the first job checks the issue author's permission level and stops immediately for everyone else).
> 2. Even for authorized users, every deployment is **paused** until the owner manually approves it through the `infomaniak-production` environment gate.

---

## How it works

```
1. Open an issue using the "Deployment Request" template
        ↓
   The "deploy" label is auto-applied → workflow starts automatically
   — OR —
   Post a "/deploy [version]" comment on an existing open issue → workflow starts
        ↓
2. Workflow checks that the trigger author has Write / Admin access
        ↓
3. Workflow acknowledges the request (posts a comment on the issue)
        ↓
4. Workflow pauses — owner receives an approval notification
        ↓
5. Owner approves (or rejects) the deployment in the Actions tab
        ↓
6. Workflow downloads the requested OGSpy release from OGSteam/ogspy
        ↓
7. Alliance name is sanitized to create a folder name and table prefix
        ↓
8. Files are deployed to alliance-specific folder via SSH (rsync)
   (config files, cache, and logs are never overwritten)
        ↓
9. If first deployment: CLI installer runs to set up the database
   Otherwise: CLI upgrader runs to update the installation
        ↓
10. Result (success / failure / rejected) is posted as a comment and the issue is closed
```

---

## One-time setup

### 1 — Required repository secrets

Go to **Settings → Secrets and variables → Actions** and add the following secrets:

| Secret | Description |
|---|---|
| `OGSPY_GITHUB_TOKEN` | Fine-grained PAT with **read** access to releases on `OGSteam/ogspy` |
| `INFOMANIAK_DEPLOY_PATH` | Absolute remote path to the base directory (e.g. `/sites/mysite.com/public_html/`). Each alliance will get its own subfolder here. |
| `INFOMANIAK_SSH_HOST` | SSH hostname from Infomaniak panel |
| `INFOMANIAK_SSH_USER` | SSH username |
| `INFOMANIAK_SSH_PASSWORD` | SSH password |
| `OGSPY_DB_HOST` | MySQL/MariaDB hostname |
| `OGSPY_DB_USER` | Database username |
| `OGSPY_DB_PASSWORD` | Database password |
| `OGSPY_DB_NAME` | Database name (all alliances share the same database with different table prefixes) |

#### Generating the GitHub PAT (`OGSPY_GITHUB_TOKEN`)

1. Go to <https://github.com/settings/personal-access-tokens/new>
2. Choose **Fine-grained token**
3. Resource owner: `OGSteam`
4. Repository access: Only `OGSteam/ogspy`
5. Permissions → Repository permissions → **Contents**: Read-only
6. Copy the token and save it as `OGSPY_GITHUB_TOKEN`

---

### 2 — GitHub Environment with approval gate

1. Go to **Settings → Environments → New environment**
2. Name it exactly: `infomaniak-production`
3. Under **Deployment protection rules**, enable **Required reviewers**
4. Add yourself (the owner) as a required reviewer
5. Save

This is what causes the workflow to pause and notify you before deploying.

---

### 3 — `deploy` label

1. Go to **Issues → Labels → New label**
2. Name: `deploy`
3. Color: choose any (e.g. `#0075ca`)
4. Save

The workflow activates when this exact label is applied to an issue, or when a `/deploy` comment is posted on an open issue.

---

## Deploying

### Option A — open a new issue (recommended for new requests)

1. Click **Issues → New issue**
2. Select the **"Deployment Request"** template (available in English and French)
3. Fill in the form:
   - **Alliance Name**: The name of your alliance (will be used to create folder and database prefix)
   - **Version**: Optional; leave blank to deploy the latest release
4. Submit — the `deploy` label is auto-applied, which starts the workflow immediately and posts an acknowledgement comment

**Important:** The alliance name will be normalized to create:
- A folder name (lowercase letters only, e.g., "Elite Alliance" → "elitealliance")
- A database table prefix (same as folder name)
- An admin username (the full alliance name as entered)

### Option B — comment command on an existing issue

Post a comment on any **open** deployment-request issue:

```
/deploy
```

Optionally include a specific version tag:

```
/deploy 4.0.2
```

The workflow starts immediately, just as if the label had been applied.  
**Option A** checks the **issue author's** permissions; **Option B** checks the **comment author's** permissions. In both cases the actor must have **Write** or **Admin** access to the repository.

---

**To approve:** go to the **Actions** tab, open the running workflow, and click **Review deployments → Approve**.

**To reject:** click **Review deployments → Reject** — the issue will be closed automatically.

---

## Alliance-specific deployments

Each alliance gets:
- **Dedicated folder**: `<INFOMANIAK_DEPLOY_PATH>/<alliance-name>/`
- **Unique table prefix**: Database tables use `<alliance-name>_` prefix (e.g., `furieux_users`, `furieux_planets`)
- **Separate configuration**: Each instance has its own `config/id.php`
- **Admin credentials**:
  - Username: Alliance name (as entered in the form)
  - Password: `ogsteam` (default, should be changed after first login)

### First deployment
When deploying a new alliance, the workflow automatically:
1. Creates the alliance folder
2. Deploys OGSpy files
3. Runs the CLI installer to set up the database
4. Creates the admin account

### Subsequent deployments
For existing alliances:
1. Updates files via rsync
2. Runs the CLI upgrader to apply database migrations

## Files protected from overwrite

The rsync deploy step **never overwrites** the following remote files (they contain instance-specific configuration or runtime data):

- `config/id.php` — database connection
- `config/key.php` — encryption key
- `config/salt` — password salt
- `install/install.lock` — installation lock file
- `cache/**` — cached data
- `logs/**` — application logs

---

## Troubleshooting

| Symptom | Likely cause |
|---|---|
| Download step fails with "No release found" | No published (non-draft) release exists on OGSteam/ogspy; publish the draft first |
| rsync step fails with "Permission denied" | Check SSH credentials: `INFOMANIAK_SSH_HOST`, `INFOMANIAK_SSH_USER`, `INFOMANIAK_SSH_PASSWORD` |
| Install CLI fails with missing secrets | Check that `OGSPY_DB_HOST`, `OGSPY_DB_USER`, `OGSPY_DB_PASSWORD`, `OGSPY_DB_NAME` are properly configured |
| Install CLI fails with DB connection error | Verify database credentials and that the database exists |
| `upgrade_cli.php` returns an error | Check the full SSH output in the Actions log; may be a PHP version or DB connectivity issue |
| Alliance name validation fails | Alliance name must contain at least one letter (numbers and special characters are removed) |
| Approval notification not received | Ensure you are set as a required reviewer on the `infomaniak-production` environment |
