# 🛡️ Rôles & Signalements — Minecraft Bedrock

Behavior pack Script API pour **Minecraft Bedrock Edition** qui ajoute un système de **rôles** (avec préfixe coloré au-dessus de la tête), un **panneau d'administration** complet en formulaires, et une commande de **signalement** conçue pour être relayée vers Discord.

![Version](https://img.shields.io/badge/version-1.0.3-blue)
![Minecraft Bedrock](https://img.shields.io/badge/Minecraft%20Bedrock-%E2%89%A5%201.21.80-brightgreen)
![Script API](https://img.shields.io/badge/@minecraft/server-2.11.0--beta-orange)
[![Discord](https://img.shields.io/badge/Discord-Rejoindre-5865F2?logo=discord&logoColor=white)](https://discord.gg/5gfpZ2452q)

---

## ✨ Fonctionnalités

- **Rôles personnalisables** : création, modification et suppression en jeu (identifiant, libellé, couleur).
- **Nametag automatique** : le rôle s'affiche au-dessus de la tête du joueur, par exemple `[Admin] Pseudo`.
- **Permissions par rôle** : 21 permissions granulaires, activables rôle par rôle.
- **Panneau d'administration** (`/rp:admin`) : chaque joueur ne voit que les actions auxquelles ses rôles donnent droit.
- **Signalements** (`/rp:report`) : accessibles à tous les joueurs, écrits dans les logs serveur dans un format prêt à parser pour un bot Discord.
- **Sanctions et modération** : kick, ban, mute, gel de joueur, whitelist.
- **Protection de la pose de blocs** (TNT par défaut) avec liste configurable.
- **Historique des signalements** et **journal d'administration** conservés dans le monde.

---

## 📦 Installation

### Monde solo / Realms / client

1. Télécharge le fichier `.mcpack` depuis les [Releases](../../releases).
2. Ouvre-le (double-clic) : Minecraft l'importe automatiquement.
3. Dans les paramètres du monde, active le pack dans **Packs de comportement**.
4. Active l'expérience **« API de script bêta »** (*Beta APIs*), indispensable pour les commandes personnalisées et la version bêta des modules.

### Serveur dédié (BDS)

1. Dézippe le pack dans `behavior_packs/RolesSignalements/` (le dossier doit contenir `manifest.json` et `scripts/`).
2. Ajoute-le à `worlds/<monde>/world_behavior_packs.json` :
   ```json
   [
     {
       "pack_id": "38b19db2-12ed-4f0b-8c8d-94b56f8a8f71",
       "version": [1, 0, 3]
     }
   ]
   ```
3. Active les Beta APIs dans les `level.dat` / paramètres du monde.
4. Vérifie que `@minecraft/server-ui` est autorisé dans `config/default/permissions.json` (`allowed_modules`).

---

## 🎮 Commandes

| Commande | Qui peut l'utiliser | Description |
|---|---|---|
| `/rp:role <add\|remove> <cible> <role>` | Niveau *Game Directors* (opérateurs) | Ajoute ou retire un tag de rôle à un ou plusieurs joueurs. |
| `/rp:report [cible] [raison]` | Tout le monde | Envoie un signalement aux modérateurs. La cible et la raison sont optionnelles. |
| `/rp:admin` | Rôles ayant l'accès au panneau | Ouvre le panneau d'administration. |

### Exemples

```mcfunction
/rp:role add @a[name="Steve"] admin
/rp:role remove "Steve" moderateur
/rp:report "Alex" "Insultes dans le chat"
/rp:admin
```

---

## 🏷️ Système de rôles

Un rôle est stocké sous forme de **tag** préfixé par `role_` (ex. `role_admin`). Les définitions (libellé, couleur, permissions) sont enregistrées dans les propriétés dynamiques du monde.

### Rôles par défaut

| ID | Libellé | Couleur | Accès au panneau |
|---|---|---|---|
| `fonda` | Fondateur | Or | ✅ (toutes les permissions, non modifiables) |
| `admin` | Admin | Rouge | ✅ |
| `moderateur` | Modérateur | Aqua | ❌ |

Tu peux créer autant de rôles que tu veux depuis **Panneau Admin → Gestion des rôles**. Couleurs disponibles : Blanc, Gris, Rouge, Or, Jaune, Vert, Aqua, Bleu, Violet, Noir.

---

## 🧰 Panneau d'administration

Les actions affichées dépendent des permissions du joueur (un joueur peut cumuler plusieurs rôles).

| Permission | Action |
|---|---|
| `assign_roles` | Attribuer ou retirer un rôle |
| `manage_roles` | Créer, modifier, supprimer des rôles |
| `manage_permissions` | Configurer les permissions de chaque rôle |
| `teleport` | Téléporter un joueur vers soi / se téléporter vers un joueur |
| `announce` | Envoyer une annonce globale |
| `private_message` | Envoyer un message privé |
| `kick` | Exclure un joueur |
| `ban` | Bannir / débannir |
| `mute` | Muter / démuter (bloque le chat) |
| `gamemode` | Changer le mode de jeu |
| `heal` | Soigner et nourrir |
| `clear_inventory` | Vider l'inventaire |
| `give_item` | Donner un objet |
| `freeze` | Geler / dégeler un joueur |
| `world_settings` | Modifier la météo et l'heure |
| `view_players` | Voir les joueurs en ligne |
| `view_stats` | Voir les statistiques du serveur |
| `view_history` | Consulter l'historique des sanctions et signalements |
| `view_admin_log` | Consulter le journal d'administration |
| `whitelist` | Gérer la whitelist |
| `block_protection` | Gérer la protection de pose de blocs |

> Les actions sensibles (suppression d'un rôle, etc.) demandent une confirmation, et chaque action admin est inscrite dans le journal.

---

## 🚨 Signalements & intégration Discord

Quand un joueur utilise `/rp:report`, le pack :

1. écrit une ligne dans les **logs du serveur** ;
2. ajoute le signalement à l'**historique** du monde (consultable dans le panneau) ;
3. confirme l'envoi au joueur.

Format de la ligne de log :

```
[REPORT] auteur="Steve" cible="Alex" raison="Insultes dans le chat" date="2026-10-15T18:42:10.123Z"
```

> ⚠️ **Le pack n'envoie rien directement à Discord.** Il écrit uniquement dans les logs. Pour recevoir les signalements sur Discord, il te faut un bot ou un script externe qui surveille la sortie du serveur, filtre les lignes `[REPORT]` et les poste via un webhook Discord.

Exemple minimal de filtrage (à adapter à ton hébergement) :

```python
import re

PATTERN = re.compile(
    r'\[REPORT\] auteur="(?P<auteur>.*?)" cible="(?P<cible>.*?)" '
    r'raison="(?P<raison>.*?)" date="(?P<date>.*?)"'
)

def parse_report(ligne: str):
    m = PATTERN.search(ligne)
    return m.groupdict() if m else None
```

---

## 🧱 Protection de la pose de blocs

Empêche les joueurs sans rôle approprié de poser certains blocs (par défaut `minecraft:tnt`).

- Activation / désactivation depuis le panneau (**Protection de blocs**).
- Ajout et retrait d'identifiants de blocs (`minecraft:tnt`, ou simplement `tnt`).
- Les rôles `fonda`, `admin` et `moderateur` peuvent toujours poser ces blocs.

---

## 💾 Données conservées

Tout est stocké dans les propriétés dynamiques du monde :

| Donnée | Limite |
|---|---|
| Définitions des rôles | — |
| Joueurs bannis / mutés / whitelist (par pseudo, insensible à la casse) | — |
| Historique des signalements | 50 dernières entrées |
| Journal d'administration | 100 dernières entrées |
| Liste de blocs interdits | — |

---

## ⚙️ Compatibilité

| Élément | Valeur |
|---|---|
| Minecraft Bedrock | ≥ 1.21.80 |
| `@minecraft/server` | 2.11.0-beta |
| `@minecraft/server-ui` | 2.3.0-beta |
| Type de pack | Behavior pack (script) |

Le pack dépend de modules en **bêta** : une mise à jour de Minecraft peut demander d'ajuster les versions dans `manifest.json`.

---

## 📁 Structure du projet

```
.
├── manifest.json
└── scripts/
    └── main.js
```

---

## 🔧 Configuration (dans `scripts/main.js`)

| Constante | Rôle |
|---|---|
| `ROLE_TAG_PREFIX` | Préfixe des tags de rôle (`role_`) |
| `REPORT_LOG_PREFIX` | Préfixe des lignes de log lues par le bot Discord (`[REPORT]`) |
| `HISTORIQUE_MAX` / `JOURNAL_ADMIN_MAX` | Taille de l'historique et du journal |
| `DEFINITIONS_PAR_DEFAUT` | Rôles créés par défaut |
| `BLOCK_PLACE_ALLOWED_ROLES` | Rôles autorisés à poser les blocs protégés |
| `BLOCK_PLACE_DEFAULT_DENYLIST` | Blocs interdits par défaut |

---

## 💬 Communauté & support

Une question, un bug, une idée de fonctionnalité ? Rejoins notre serveur Discord :

[![Rejoindre le Discord](https://img.shields.io/badge/Discord-Rejoindre%20le%20serveur-5865F2?style=for-the-badge&logo=discord&logoColor=white)](https://discord.gg/5gfpZ2452q)

Pour les bugs reproductibles, tu peux aussi ouvrir une [issue](../../issues) sur GitHub.

---

## 🤝 Contribuer

Les issues et pull requests sont les bienvenues. Pour proposer une amélioration :

1. Fork le dépôt
2. Crée une branche (`git checkout -b feature/ma-fonctionnalite`)
3. Commit tes changements
4. Ouvre une Pull Request

---

## 📄 Licence
[MIT](https://choosealicense.com/licenses/mit/)).

## ✍️ Auteur
PAR AXDEV 
À compléter — pense à remplacer `"Toi"` dans le champ `metadata.authors` du `manifest.json`.
