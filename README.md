# TicketDesk — Guide d'installation complet

## 🗂️ Fichiers du projet
```
ticketdesk/
├── index.html      ← L'application complète
├── manifest.json   ← Config PWA (icône, nom, couleur)
├── sw.js           ← Service Worker (mode hors-ligne)
└── icons/
    ├── icon-192.png  ← À créer (voir ci-dessous)
    └── icon-512.png  ← À créer (voir ci-dessous)
```

---

## ÉTAPE 1 — Héberger sur GitHub Pages (gratuit, 5 min)

### 1.1 Créer un compte GitHub
→ https://github.com → Sign up (gratuit)

### 1.2 Créer un nouveau repository
- Clique "New repository"
- Nom : `ticketdesk`
- Cocher "Public"
- Cliquer "Create repository"

### 1.3 Uploader les fichiers
- Clique "uploading an existing file"
- Glisse tous les fichiers (index.html, manifest.json, sw.js + dossier icons/)
- Clique "Commit changes"

### 1.4 Activer GitHub Pages
- Settings → Pages
- Source : "Deploy from a branch"
- Branch : `main` / `root`
- Save

→ Ton app sera disponible sur : `https://TON_PSEUDO.github.io/ticketdesk/`

---

## ÉTAPE 2 — Créer les icônes PWA

### Option simple (en ligne)
1. Va sur https://www.canva.com
2. Crée une image 512x512px avec fond noir (#0d0d0d) et 🎫 en orange (#FF5E1A)
3. Exporte en PNG → renomme en `icon-512.png`
4. Redimensionne en 192x192 → `icon-192.png`
5. Mets les dans le dossier `icons/` sur GitHub

---

## ÉTAPE 3 — Configurer Gmail OAuth

### 3.1 Créer un projet Google Cloud
1. Va sur https://console.cloud.google.com
2. Clique "Nouveau projet" → nom : `TicketDesk`
3. Sélectionne ce projet

### 3.2 Activer l'API Gmail
1. Menu → "API et services" → "Bibliothèque"
2. Cherche "Gmail API" → Activer

### 3.3 Créer les identifiants OAuth
1. Menu → "API et services" → "Identifiants"
2. "+ Créer des identifiants" → "ID client OAuth"
3. Type : "Application Web"
4. Nom : `TicketDesk`
5. Origines JavaScript autorisées : `https://TON_PSEUDO.github.io`
6. URI de redirection : `https://TON_PSEUDO.github.io/ticketdesk/index.html`
7. Clique "Créer"
8. Copie le **Client ID** (format : xxxxx.apps.googleusercontent.com)

### 3.4 Configurer l'écran de consentement
1. Menu → "Écran de consentement OAuth"
2. Type : "Externe"
3. Remplis : Nom de l'app, email
4. Scopes : ajoute ces deux scopes :
   - `https://www.googleapis.com/auth/gmail.readonly`
   - `https://www.googleapis.com/auth/drive.appdata`
5. Ajoute ton email en "Utilisateur test"

### 3.5 Coller le Client ID dans index.html
Ouvre `index.html`, ligne ~230, remplace :
```js
const GOOGLE_CLIENT_ID = 'VOTRE_CLIENT_ID_ICI';
```
Par :
```js
const GOOGLE_CLIENT_ID = '123456789-abc.apps.googleusercontent.com';
```
Puis re-upload le fichier sur GitHub.

---

## ÉTAPE 4 — Installer sur iPhone (PWA)

1. Ouvre Safari sur ton iPhone
2. Va sur `https://TON_PSEUDO.github.io/ticketdesk/`
3. Tape le bouton **Partager** (carré avec flèche ↑)
4. Scroll → "Sur l'écran d'accueil"
5. Nom : `TicketDesk` → Ajouter

→ L'icône apparaît sur ton écran d'accueil, l'app s'ouvre en plein écran comme une vraie app native.

---

## UTILISATION

### Scanner tes mails
1. Onglet 📧 Gmail
2. "Connecter Gmail" → autorise l'accès en lecture
3. "Scanner les billets" → TicketDesk cherche les mails Ticketmaster, Fnac, SDF
4. Coche les billets détectés → "Importer sélection"

### Générer le message WTS
1. Onglet 💬 WTS
2. Le message est généré automatiquement depuis tes billets "En stock"
3. "Copier pour Discord" → colle directement dans ton serveur

### Données
Les données sont stockées localement sur ton téléphone (localStorage).
Elles persistent entre les sessions et fonctionnent hors-ligne.

---

## ⚠️ Notes importantes

- **Gmail** : accès lecture seule, tes mails ne sont jamais modifiés
- **Drive** : les données sont dans le dossier `appDataFolder` — invisible dans ton Drive normal, privé, accessible uniquement par TicketDesk
- **Sync automatique** : chaque modification (ajout, édition, suppression) déclenche une sync Drive 1.5s après
- **Multi-appareils** : ouvre l'app sur ton PC → connecte Google → clique "Récupérer depuis Drive" → toutes tes données s'affichent
- **Token Gmail/Drive** : expire après 1h, reconnecte-toi si nécessaire
- **Hors-ligne** : toutes les fonctions marchent sans internet sauf le scan Gmail et la sync Drive
