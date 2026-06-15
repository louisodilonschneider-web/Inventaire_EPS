# 🏃 EPS Inventaire — Gestion du matériel sportif

Application web mono-fichier HTML pour inventorier, suivre et commander le matériel sportif d'un établissement scolaire. Les données sont synchronisées en temps réel via **Firebase Realtime Database** : plusieurs appareils (téléphone, tablette, ordinateur) peuvent consulter et modifier l'inventaire simultanément.

---

## Démarrage rapide

1. Télécharger le fichier `Inventaire_EPS_V03_index.html`
2. L'ouvrir dans un navigateur (Chrome, Firefox, Safari, Edge)
3. Le bouton de connexion en bas du menu passe au **vert** dès que Firebase répond
4. C'est prêt — pas d'installation, pas de serveur

> L'application fonctionne aussi hors-ligne : les données sont sauvegardées localement en secours et resynchronisées dès le retour du réseau.

---

## Fonctionnalités

### 📊 Récapitulatif
Vue d'ensemble de l'inventaire avec compteurs globaux (articles, unités, états). Les articles sont regroupés par **sport** puis par **sous-catégorie**, avec barres de progression colorées (✅ Correct / ⚠️ Dégradé / ❌ Inutilisable). Tri personnalisable dans chaque groupe.

### ➕ Ajouter du matériel
Formulaire complet avec :
- Nom, quantité, sport, sous-catégorie, référence Casal, date d'entrée, notes
- **État par unité** : chaque unité est cliquable et cycle entre Correct → Dégradé → Inutilisable. Augmenter la quantité conserve les états déjà saisis.
- **Photos** : bouton galerie (fichiers) + bouton caméra directe (téléphone). Les images sont compressées automatiquement (< 200 Ko) et stockées en base64 dans Firebase — aucun service de stockage payant requis.

### 🔍 Rechercher
Deux onglets : catalogue **Casal Sport** (filtrable par sport) et **Mon inventaire** (recherche par nom, sport, référence). Depuis le catalogue, ajout direct à l'inventaire ou à la commande en un clic.

### 🛒 Commander
Panier de commande alimenté automatiquement depuis les articles inutilisables, complété manuellement via le catalogue Casal ou en article libre. Affichage du total indicatif. La commande passée rejoint l'historique.

### 📋 Historique
Journal chronologique de toutes les opérations : ajouts, suppressions, réparations, commandes passées et arrivées. Une commande "en cours" peut être marquée comme arrivée, ce qui remet automatiquement les unités inutilisables correspondantes à l'état Correct.

### 🔧 Réparation
Liste des articles dégradés ou inutilisables, triés par priorité (inutilisable > dégradé). Le nom affiché assemble la **sous-catégorie + nom** (ex : *Raquettes bleues*). Saisie du nombre d'unités réparées pour chaque article.

---

## Synchronisation multi-appareils

L'application utilise **Firebase Realtime Database** en écoute continue (`onValue`). Toute modification faite sur un appareil se propage instantanément sur tous les autres sans rechargement.

Un **indicateur de connexion** est visible en permanence en bas du menu latéral :

| Couleur | Signification |
|---|---|
| 🟢 Vert (pulsant) | Connecté et synchronisé |
| 🟡 Jaune | Sauvegarde en cours |
| 🔴 Rouge | Déconnecté (mode hors-ligne) |

Sur téléphone (menu réduit), seul le cercle coloré est affiché. Sur tablette et ordinateur, le texte est visible.

---

## Photos

Les photos sont prises ou importées directement depuis le formulaire d'ajout :

- **📁 Galerie / Fichier** — sélection depuis les fichiers de l'appareil
- **📷 Prendre une photo** — ouvre directement la caméra arrière sur mobile

Les images sont converties en **base64** par le navigateur via `FileReader.readAsDataURL()` puis compressées (canvas JPEG 80 %, max 200 Ko) avant d'être stockées comme simple texte dans Firebase. Pas besoin de Firebase Storage (payant).

Dans l'inventaire, jusqu'à 3 vignettes sont affichées par article. Un clic ouvre une visionneuse plein écran avec navigation ◀ ▶.

---

## Configuration Firebase

### Projet utilisé
```
Nom       : Inventaire materiel 2
Base URL  : https://inventaire-materiel-2-default-rtdb.europe-west1.firebasedatabase.app
Région    : Belgique (europe-west1)
```

### Règles de sécurité (onglet Règles dans la console Firebase)
L'accès est ouvert en lecture/écriture pour tout le réseau local. Remplacer les règles par défaut par :

```json
{
  "rules": {
    "eps_db": {
      ".read": true,
      ".write": true
    }
  }
}
```

> Ces règles conviennent pour un usage scolaire interne. Si l'URL de la base venait à être publique, il faudrait ajouter une authentification Firebase.

### Structure des données dans Firebase
```
eps_db/
├── inventaire/          ← tableau d'articles
│   ├── id              (timestamp string)
│   ├── nom
│   ├── qty
│   ├── unites[]        (ex : ["Correct","Dégradé","Correct"])
│   ├── ref             (référence Casal)
│   ├── sport
│   ├── souscat
│   ├── date
│   ├── notes
│   └── images[]        (chaînes base64 JPEG)
├── commande/            ← panier en cours
└── historique/          ← journal des événements
```

---

## Catalogue Casal Sport

Le fichier embarque un extrait du catalogue Casal Sport (~300 références) couvrant : Athlétisme, Badminton, Tennis de table, Football, Handball, Rugby, Hockey, Natation, Fitness, Musculation, Escalade, Orientation, Éveil/Motricité, Aménagement, Outdoor, Textile, Bagagerie.

---

## Compatibilité

| Appareil | Navigateur | Statut |
|---|---|---|
| Ordinateur | Chrome, Edge, Firefox, Safari | ✅ |
| Tablette | Safari iOS, Chrome Android | ✅ |
| Téléphone | Safari iOS, Chrome Android | ✅ (caméra directe supportée) |

---

## Versions

| Version | Nouveautés |
|---|---|
| V01 | Inventaire local (localStorage), catalogue Casal |
| V02 | Groupement par sport/sous-catégorie, réparation, historique |
| V03 | Firebase Realtime DB, synchronisation multi-appareils, photos base64, indicateur connexion, visionneuse photos, compression images |
