# OGSpy Deploy

Deploy OGSpy for your alliance with one GitHub issue.

![Status](https://img.shields.io/badge/status-active-2ea44f)
![Hosting](https://img.shields.io/badge/hosting-Infomaniak-0b57d0)
![Approval](https://img.shields.io/badge/deployment-manual%20approval-orange)

Fast setup, isolated instances per alliance, and safe update flow.

## Quick Navigation

- Français: see `Guide FR`
- English: see `Guide EN`
- Maintainers: see `README.developer.md`

---

## Guide FR

### Pourquoi ce dépôt

Ce dépôt permet de demander, déployer et mettre à jour OGSpy sans intervention manuelle sur les fichiers serveurs.

### Aperçu

- Une instance OGSpy dédiée par alliance
- Une URL directe: `https://ogspy.fr/<alliance>`
- Un compte administrateur créé automatiquement
- Les mises à jour applicatives gérées par OGSpy

### Démarrage rapide

1. Ouvrez `Issues -> New issue` dans ce dépôt.
2. Choisissez le modèle `Deployment Request`.
3. Remplissez le champ `Nom de l'Alliance`.
4. Envoyez le ticket.
5. Attendez la validation manuelle du propriétaire.

### Résultat du déploiement

Le commentaire final contient:
- l'URL de votre instance
- les identifiants admin
- les détails du déploiement

### Identifiants admin initiaux

- Utilisateur: nom de l'alliance saisi dans le ticket
- Mot de passe: `ogsteam`

Important:
- Changez le mot de passe immédiatement après la première connexion.

### Mises à jour

Les mises à jour sont gérées par OGSpy lui-même (routine interne d'upgrade).

### FAQ rapide (FR)

Q: Qui peut lancer un déploiement?
A: Seuls les collaborateurs autorisés du dépôt (write/admin), avec validation manuelle du propriétaire.

Q: Le déploiement écrase-t-il ma configuration?
A: Non, les fichiers sensibles de configuration et de runtime sont préservés par le workflow.

Q: Dois-je changer le mot de passe admin par défaut?
A: Oui, immédiatement après la première connexion.

### Liens utiles

- Documentation OGSpy: https://wiki.ogsteam.eu/
- Discord OGSteam: https://discord.gg/Azcb67b

---

## Guide EN

### Why this repository

This repository lets you request, deploy, and update OGSpy without manual server file operations.

### Overview

- One dedicated OGSpy instance per alliance
- A direct URL: `https://ogspy.fr/<alliance>`
- An admin account created automatically
- Application updates managed by OGSpy itself

### Quick start

1. Open `Issues -> New issue` in this repository.
2. Select the `Deployment Request` template.
3. Fill in the `Alliance Name` field.
4. Submit the issue.
5. Wait for the owner's manual approval.

### Deployment result

The final comment includes:
- your instance URL
- admin credentials
- deployment details

### Initial admin credentials

- Username: alliance name entered in the issue
- Password: `ogsteam`

Important:
- Change the password immediately after first login.

### Updates

Updates are handled by OGSpy itself (internal upgrade routine).

### Quick FAQ (EN)

Q: Who can trigger a deployment?
A: Only authorized repository collaborators (write/admin), with manual owner approval.

Q: Does deployment overwrite my instance config?
A: No. Sensitive config and runtime files are preserved by the workflow.

Q: Should I change the default admin password?
A: Yes, immediately after first login.

### Helpful links

- OGSpy documentation: https://wiki.ogsteam.eu/
- OGSteam Discord: https://discord.gg/Azcb67b

---

## For Maintainers

Developer and infrastructure documentation is available in:

- [README.developer.md](README.developer.md)
