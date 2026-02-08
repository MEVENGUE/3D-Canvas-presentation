# Guide de déploiement — Vercel & Railway

Ce projet est structuré en **monorepo** : frontend (Next.js) sur Vercel, backend (Express) sur Railway.

---

## 1. Déployer le Backend sur Railway

### Étapes

1. Créez un compte sur [railway.app](https://railway.app)
2. **New Project** → **Deploy from GitHub** (connectez votre repo)
3. **Settings** du service :
   - **Root Directory** : `backend`
   - **Build Command** : (laisser vide, `railway.json` gère)
   - **Start Command** : (laisser vide, `railway.json` gère)
4. **Variables** : ajoutez `FRONTEND_URL` = `https://votre-app.vercel.app` (à mettre à jour après le déploiement frontend)
5. Déployez et notez l’URL du backend (ex. `https://xxx.railway.app`)

---

## 2. Déployer le Frontend sur Vercel

### Assets 3D (modèles .glb)

Les modèles 3D ne sont pas dans le repo (pour limiter la taille). Pour que l’app fonctionne :

1. Créez un bucket R2 sur Cloudflare Dashboard → R2
2. Structurez les dossiers : `drones/`, `robots/`, `logos/`, `misc/`
3. Uploadez vos fichiers `.glb` (ex. `robots/rob_service_hun.glb`)
4. Activez l'accès public et configurez CORS pour votre domaine Vercel
5. Récupérez l'URL publique et définissez-la dans `NEXT_PUBLIC_R2_BASE_URL`

### Étapes

1. Créez un compte sur [vercel.com](https://vercel.com)
2. **Add New Project** → importez votre repo GitHub
3. **Configure Project** :
   - **Root Directory** : `frontend` (obligatoire)
   - **Framework Preset** : Next.js (détecté automatiquement)
   - **Build Command** : `npm run build`
   - **Output Directory** : (laisser par défaut)
4. **Environment Variables** :
   - `NEXT_PUBLIC_R2_BASE_URL` = `https://pub-xxx.r2.dev` (URL publique Cloudflare R2 — **obligatoire** pour les modèles 3D)
   - `OPENAI_API_KEY` = `sk-...` (clé API OpenAI pour le chat assistant — [platform.openai.com](https://platform.openai.com/api-keys))
   - `NEXT_PUBLIC_API_URL` = `https://votre-backend.railway.app` (URL du backend Railway — optionnel)
5. **CORS R2** : Configurez l'origine Vercel dans les paramètres CORS du bucket R2
6. Déployez

---

## 3. Finaliser la configuration

1. **Railway** : mettez à jour `FRONTEND_URL` avec l’URL Vercel finale (ex. `https://votre-projet.vercel.app`)
2. Redéployez le backend Railway pour appliquer le nouveau CORS

---

## Variables d’environnement

| Plateforme | Variable | Description |
|------------|----------|-------------|
| **Vercel** | `NEXT_PUBLIC_R2_BASE_URL` | URL publique Cloudflare R2 (modèles 3D) |
| **Vercel** | `OPENAI_API_KEY` | Clé API OpenAI pour le chat assistant |
| **Vercel** | `NEXT_PUBLIC_API_URL` | URL du backend Railway (optionnel) |
| **Railway** | `FRONTEND_URL` | URL du frontend Vercel (pour CORS) |
| **Railway** | `PORT` | Défini automatiquement par Railway |

---

## Vérification

- **Backend** : `https://votre-backend.railway.app/api/health` → `{"status":"ok"}`
- **Frontend** : ouverture de l’URL Vercel et test de la page Explorer
