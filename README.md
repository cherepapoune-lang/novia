# NOVIA — ton assistante IA

Une assistante francophone qui réunit plusieurs modèles d'IA derrière **une seule personnalité**.

- **Mode Auto** : NOVIA choisit toute seule le modèle le plus adapté à ta question.
- **Mode Fusion ✨** : trois modèles répondent, NOVIA compare et te donne **une seule réponse**, en signalant les désaccords.
- **Voix** : parle-lui avec le micro, et elle peut te lire ses réponses.
- **Installable sur PC et téléphone** (PWA), même hors-ligne pour l'interface.
- **0 €** : gratuit, sans abonnement, sans carte bancaire.

---

## 1. Ce dont tu as besoin

Trois comptes **gratuits** (environ 10 minutes au total) :

| Compte | Pour quoi | Où le créer |
|---|---|---|
| **Google AI Studio** | La clé API gratuite qui fait parler NOVIA | https://aistudio.google.com/apikey |
| **GitHub** | Ranger le code | https://github.com |
| **Vercel** | Mettre le site en ligne (gratuit) | https://vercel.com |

> Tu peux ajouter **Groq** (https://console.groq.com/keys) pour un 2e cerveau gratuit et plus de Fusion. Facultatif.

---

## 2. Obtenir la clé gratuite (Gemini)

1. Va sur **https://aistudio.google.com/apikey**
2. Connecte-toi avec ton compte Google.
3. Clique sur **Create API key** (Créer une clé API).
4. **Copie** la clé (elle ressemble à `AIza...`). Garde-la précieusement.
5. **Aucune carte bancaire** n'est demandée.

---

## 3. Mettre le code sur GitHub

1. Crée un compte sur https://github.com (gratuit).
2. En haut à droite, clique **+** → **New repository**.
3. Donne un nom (ex. `novia`), laisse-le **Public** ou **Private**, **sans** cocher « Add a README ».
4. Clique **Create repository**.
5. Sur la page suivante, clique **« uploading an existing file »** (en bleu, au centre).
6. **Glisse-dépose** tout le contenu du dossier `novia` (tous les fichiers et sous-dossiers : `app`, `components`, `lib`, `public`, `package.json`…). **Surtout pas** les dossiers `node_modules` ni `.next` s'ils existent.
7. En bas, clique **Commit changes**.

---

## 4. Mettre en ligne avec Vercel

1. Crée un compte sur https://vercel.com (compte **GitHub**).
2. Clique **Add New… → Project**.
3. Choisis le dépôt **novia** (autorise Vercel à lire ton GitHub).
4. Vercel détecte tout seul « Next.js ». Ne change rien.
5. Déroule la section **Environment Variables** et ajoute :

   | Name | Value |
   |---|---|
   | `GEMINI_API_KEY` | la clé `AIza...` que tu as copiée |

6. Clique **Deploy**.

Au bout d'une minute, tu obtiens un lien du type `https://novia-xxxx.vercel.app`. **C'est ton app NOVIA, en ligne.**

Tu peux aussi la mettre sur ton téléphone : ouvre ce lien dans Chrome/Safari → menu → **« Ajouter à l'écran d'accueil »**.

---

## 5. Personnaliser

- **La voix de NOVIA** : dans l'app, bouton **Réglages** → Directe / Douce / Complice.
- **La personnalité** : c'est le fichier [`lib/prompt.ts`](lib/prompt.ts) (le texte entre `IDENTITY`, `TONE_RULES` et `RULES`). Modifie, puis redéploie (commit sur GitHub = nouvelle version automatique sur Vercel).
- **Les modèles** : changements via les variables d'environnement (`GEMINI_MODEL`, `GROQ_MODEL`…).

---

## 6. Ajouter d'autres cerveaux (gratuit)

Plus tu ajoutes de clés, plus NOVIA a de « cerveaux » pour la Fusion :

| Fournisseur | Clé | Modèle par défaut |
|---|---|---|
| Gemini | `GEMINI_API_KEY` | `gemini-2.5-flash` |
| Groq | `GROQ_API_KEY` | `openai/gpt-oss-120b` |
| OpenRouter | `OPENROUTER_API_KEY` | `meta-llama/llama-3.3-70b-instruct:free` |
| Cloudflare | `CLOUDFLARE_ACCOUNT_ID` + `CLOUDFLARE_API_TOKEN` | `@cf/meta/llama-3.3-70b-instruct-fp8-fast` |

> ⚠️ **Limites gratuites** : chaque fournisseur offre un quota gratuit *par jour* (quelques centaines de messages). C'est suffisant pour une beta. Les noms de modèles changent parfois : si un cerveau ne répond plus, remplace le nom du modèle dans la variable correspondante.

---

## 7. Pour les développeurs

```bash
npm install
cp .env.example .env.local   # puis mets ta clé dedans
npm run dev                  # http://localhost:3000
npm run build                # build de production
```

### Structure

- `app/api/chat/route.ts` — la réponse normale (routeur + secours automatique).
- `app/api/fusion/route.ts` — le « conseil des IA » (3 modèles + synthèse).
- `app/api/health/route.ts` — état des clés configurées + test.
- `lib/providers.ts` — les 4 fournisseurs (tous via leur API compatible OpenAI).
- `lib/router.ts` — le choix du modèle selon la question.
- `lib/prompt.ts` — la personnalité de NOVIA + le prompt de synthèse.
- `components/` — l'interface (chat, conseil, compositeur, Markdown maison).

### Points d'attention

- **Les clés ne sortent jamais du serveur** : elles ne sont lues que dans les routes API.
- Aucune base de données externe : l'historique est stocké **sur l'appareil** de l'utilisateur (`localStorage`).
- Le plan gratuit **Vercel Hobby** est réservé à un usage personnel/non commercial. Pour un usage public payant, passe sur Vercel Pro ou migre vers Cloudflare Pages.
