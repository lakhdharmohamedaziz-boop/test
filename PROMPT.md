# Prompt — Application mobile « VICE MODELS » (Android / iOS)

> Copie-colle tout ce qui se trouve sous la ligne ci-dessous dans ton assistant IA de code (Claude Code, Cursor, etc.).
> Le prompt est découpé en sections numérotées pour que l'IA puisse les implémenter étape par étape.

---

## 0. Rôle et objectif

Tu es un développeur senior React Native / Expo spécialisé en UI animée. Construis une application mobile **Android + iOS** nommée **VICE MODELS** : une plateforme communautaire (forum + annonces) dédiée aux **modèles féminines professionnelles majeures** (photo, mode, défilé, publicité, clip, événementiel) et aux professionnels qui les recrutent (photographes, agences, marques, directeurs de casting).

L'app doit permettre de :
- parcourir des profils de modèles classés par catégories (style, spécialité, région, caractéristiques physiques, origine auto‑déclarée, etc.) ;
- publier et consulter des **annonces** (castings, shootings, collaborations TFP, missions rémunérées) ;
- trouver les annonces via une **carte interactive de la France** (régions → départements → villes) ;
- effectuer une **recherche avancée** multi‑filtres avec une UI moderne et animée ;
- **commenter** profils, annonces et sujets du forum ;
- souscrire à un **compte VIP** donnant accès à des fonctionnalités premium.

Direction artistique : **GTA Vice City** — néons rose/cyan, coucher de soleil rétro années 80, palmiers, typographie **Pricedown** (police des titres GTA) et un script cursif type « Vice City ».

Stockage des données : **JSON uniquement pour l'instant** (pas de vraie base de données), mais avec une couche d'accès aux données abstraite pour pouvoir migrer plus tard vers Supabase / Firebase / PostgreSQL sans réécrire l'UI.

---

## 1. Stack technique (composants récents, 2025‑2026)

| Domaine | Choix |
|---|---|
| Framework | **Expo SDK (dernière version stable)** + **React Native New Architecture** (Fabric / TurboModules) activée |
| Langage | **TypeScript** strict |
| Navigation | **Expo Router** (routing par fichiers, typed routes, deep links) |
| Animations | **react-native-reanimated v3+**, **Moti**, **react-native-gesture-handler**, **Lottie** (`lottie-react-native`) |
| Graphismes / effets | **@shopify/react-native-skia** (néons, glow, dégradés animés, shaders, grain VHS), **expo-linear-gradient**, **expo-blur** |
| Listes | **@shopify/flash-list** (grilles et listes performantes) |
| Images | **expo-image** (cache, blurhash, transitions) |
| Bottom sheets | **@gorhom/bottom-sheet** v5 |
| État global | **Zustand** (+ middleware `persist` via MMKV) |
| Données serveur | **@tanstack/react-query** v5 (cache, pagination infinie, optimistic updates) |
| Stockage local rapide | **react-native-mmkv** |
| Formulaires | **react-hook-form** + **zod** (validation) |
| Carte | **SVG interactif de la France** (`react-native-svg`) pour régions/départements + **react-native-maps** (ou **MapLibre** `@maplibre/maplibre-react-native` avec style sombre custom) pour la vue détaillée avec clusters |
| Recherche | **Fuse.js** (recherche floue côté client sur les données JSON) |
| Haptique | **expo-haptics** |
| Fonts | **expo-font** |
| Médias | **expo-image-picker**, **expo-image-manipulator** (compression avant upload) |
| Auth (mock) | JWT simulé + **expo-secure-store** pour le token |
| Paiement VIP (mock) | écran d'abonnement simulé ; prévoir l'intégration future de **RevenueCat** (`react-native-purchases`) pour les achats in‑app Apple/Google |
| Notifications | **expo-notifications** (prévu, mock en local) |
| i18n | **i18next** + **react-i18next** (FR par défaut, EN en option) |
| Qualité | ESLint, Prettier, **Jest** + **@testing-library/react-native** |
| Build | **EAS Build** / **EAS Update** |

---

## 2. Direction artistique « Vice City »

### 2.1 Palette (tokens dans `src/theme/colors.ts`)

```ts
export const colors = {
  bgDeep:      '#0B0221', // nuit violette
  bgCard:      '#1A0B2E',
  bgElevated:  '#2A1052',
  neonPink:    '#FF2E97',
  neonCyan:    '#00F0FF',
  sunsetOrange:'#FF8C42',
  sunsetYellow:'#FFD23F',
  palmPurple:  '#7B2CBF',
  vipGold:     '#F5C542',
  text:        '#FFFFFF',
  textMuted:   '#B8A9D9',
  danger:      '#FF3B5C',
  success:     '#2BFFB1',
};
export const gradients = {
  sunset:  ['#FF2E97', '#FF8C42', '#FFD23F'],
  neon:    ['#FF2E97', '#7B2CBF', '#00F0FF'],
  night:   ['#0B0221', '#1A0B2E', '#2A1052'],
  vip:     ['#F5C542', '#FF8C42', '#FF2E97'],
};
```

### 2.2 Typographie

- **Titres / logos / prix / badges** : **Pricedown** (`assets/fonts/pricedown.ttf`) — police emblématique des titres GTA. Toujours en MAJUSCULES, avec contour noir épais (stroke via Skia ou `textShadow` multiples) + ombre portée.
- **Sous‑titres / accroches** : script cursif rétro type « Vice City » (ex. une police script 80s libre de droits, à placer dans `assets/fonts/`).
- **Texte courant** : **Inter** ou **Rubik** (lisibilité).
- ⚠️ Vérifier la licence de chaque police avant publication sur les stores (Pricedown est gratuite pour usage personnel ; prévoir une licence commerciale ou une alternative pour la mise en production).

### 2.3 Effets et ambiance

- Fond animé : **coucher de soleil rétro** (soleil rayé, grille néon en perspective qui défile, silhouettes de palmiers) dessiné en **Skia**, animé en boucle légère (respecter `reduceMotion`).
- **Glow néon** sur les boutons et bordures actives (blur + shadow colorée Skia).
- **Effet scanlines / grain VHS** très subtil en overlay (désactivable dans les réglages).
- Transitions d'écran : **shared element transitions** Reanimated (photo de carte → photo plein écran du profil).
- Micro‑interactions : bouton qui « pulse » au tap, haptique légère, ripple néon.
- Écran de chargement / splash : logo **VICE MODELS** en Pricedown qui apparaît avec un « flash » néon (Lottie ou Skia).
- Badges et prix affichés « à la GTA » : ex. **`$ 250`** en Pricedown vert/jaune avec contour noir, comme l'affichage de l'argent dans le jeu.
- Popups de succès type « MISSION PASSED » (ex. « ANNONCE PUBLIÉE ! » / « RESPECT + ») en Pricedown, plein écran, animées.

### 2.4 Composants UI à créer (`src/components/ui/`)

`NeonButton`, `NeonInput`, `GlowCard`, `PricedownText` (avec stroke), `GtaPrice`, `VipBadge`, `Chip`/`FilterChip` animés, `RangeSlider` double curseur, `AnimatedTabBar` (onglets avec indicateur néon glissant), `SkeletonLoader` (shimmer néon), `EmptyState` (illustration Lottie), `Toast` style GTA, `MissionPassedModal`, `Avatar` avec anneau VIP doré animé, `RatingStars`, `SunsetBackground` (Skia), `ScanlineOverlay`.

---

## 3. Fonctionnalités détaillées

### 3.1 Authentification et comptes

- Inscription / connexion email + mot de passe (mock JSON), écran d'onboarding animé (3 slides).
- **Vérification de majorité obligatoire** : date de naissance + case de confirmation « J'ai 18 ans ou plus » ; refuser l'inscription si < 18 ans.
- Types de compte : `model` (modèle), `pro` (photographe / agence / marque), `member` (simple membre du forum), `admin`.
- Profil modèle : pseudo, photos (galerie 1 à 12 photos, réordonnables par drag & drop), bio, ville/département/région, âge, taille, mensurations (optionnel), couleur des yeux / cheveux, spécialités, expérience, langues parlées, disponibilités, liens portfolio / Instagram, tarifs indicatifs pour prestations photo/vidéo.
- **Origine / type physique** : champ **auto‑déclaré, facultatif**, avec consentement explicite (voir § 6 RGPD). Jamais déduit ou attribué par quelqu'un d'autre.
- Badge **« Profil vérifié »** (selfie + pièce d'identité, workflow mock géré par un admin).

### 3.2 Système VIP

Niveaux : `FREE`, `VIP` (mensuel), `VIP GOLD` (annuel). Avantages :

| Fonction | FREE | VIP | VIP GOLD |
|---|---|---|---|
| Annonces actives | 1 | 10 | Illimité |
| Mise en avant (« À la une ») | – | 1 / semaine | 1 / jour |
| Filtres avancés | Basiques | Tous | Tous |
| Voir qui a consulté mon profil | – | ✓ | ✓ |
| Messagerie privée | Limité | ✓ | ✓ |
| Badge animé | – | Néon rose | Or animé |
| Sans publicité | – | ✓ | ✓ |
| Thème d'app exclusif | – | – | ✓ |

- Écran **« VIP Store »** inspiré des boutiques du jeu (cartes de prix en Pricedown, effet brillance qui balaye la carte).
- Achat simulé : met à jour `user.vip = { tier, since, expiresAt }` dans le JSON.
- Garde de navigation `requireVip(tier)` pour les écrans/fonctions premium, avec écran d'upsell animé.

### 3.3 Catégories et forum

- **Catégories de profils** (filtrables et combinables) : spécialité (mode, beauté, lingerie *non explicite*, fitness, commercial, artistique, plus‑size, défilé, cosplay…), style, origine auto‑déclarée, tranche d'âge, région, niveau d'expérience, VIP / vérifiée.
- **Forum** : sections (Conseils shooting, Castings, Portfolio review, Matériel photo, Juridique & contrats, Discussions libres). Sujets → réponses, épinglage, verrouillage, likes, tri (récent / populaire / sans réponse).
- Écran d'accueil : carrousel « À LA UNE » (VIP), grille des catégories en cartes néon, dernières annonces, sujets chauds du forum.

### 3.4 Annonces

- Création via un **formulaire multi‑étapes animé** (stepper néon) :
  1. Type (casting, shooting rémunéré, TFP / collaboration, événement, recherche de photographe…)
  2. Titre, description, photos
  3. Localisation : sélection sur la carte **ou** autocomplétion ville (liste des communes en JSON) → enregistre `region`, `department`, `city`, `lat`, `lng`
  4. Date(s), durée, rémunération (affichée façon GTA `$`), profil recherché (critères)
  5. Aperçu → publication → modale **« ANNONCE PUBLIÉE ! »**
- Statuts : `draft`, `pending` (modération), `active`, `expired`, `rejected`.
- Actions : modifier, dupliquer, booster (VIP), archiver, signaler.
- Candidature à une annonce (bouton « POSTULER ») avec message.

### 3.5 Carte interactive de la France

- **Niveau 1 – France** : carte SVG des **13 régions métropolitaines** (+ DROM en encart). Chaque région a une couleur dont l'intensité dépend du nombre d'annonces (heatmap néon). Tap → zoom animé vers la région.
- **Niveau 2 – Région** : départements de la région, avec compteur d'annonces en badge Pricedown. Tap → liste filtrée + bouton « Voir sur la carte ».
- **Niveau 3 – Carte détaillée** : MapLibre / react-native-maps avec **style sombre néon custom**, marqueurs personnalisés (pin rose, pin doré pour VIP), **clustering**, bottom sheet qui affiche l'annonce sélectionnée.
- Bouton « Autour de moi » (`expo-location`, avec demande de permission).
- La sélection sur la carte se synchronise avec les filtres de recherche (store Zustand partagé).
- Sources : GeoJSON des régions et départements (open data IGN / data.gouv.fr) simplifié et converti en paths SVG dans `src/data/geo/`.

### 3.6 Moteur de recherche moderne

- Barre de recherche en haut de l'écran qui **s'étire avec animation** au focus (Reanimated layout animations), glow néon, placeholder animé qui fait défiler des suggestions (« Shooting à Marseille… », « Modèle fitness Lyon… »).
- **Recherche instantanée** (debounce 250 ms) avec Fuse.js sur titres, descriptions, villes, tags.
- Suggestions + historique récent + recherches populaires en chips.
- **Recherche vocale** (optionnel, `expo-speech-recognition`).
- **Recherche avancée** dans un bottom sheet plein écran avec sections repliables animées :
  - Type de résultat : profils / annonces / sujets forum
  - Localisation : région, département, ville, rayon (km) via slider
  - Âge (range), taille (range)
  - Origine auto‑déclarée (multi‑sélection)
  - Couleur cheveux / yeux
  - Spécialités (multi)
  - Expérience (débutante → pro)
  - Langues
  - Rémunération (range, affichée en `$` style GTA)
  - Disponibilité (date picker)
  - Profil vérifié uniquement, VIP uniquement, avec photos uniquement
  - Tri : pertinence, plus récent, plus proche, mieux noté, rémunération
- Compteur de résultats **animé** en temps réel (« 128 RÉSULTATS ») pendant qu'on ajuste les filtres.
- Filtres actifs affichés en chips supprimables sous la barre.
- **Recherches sauvegardées** + alerte (VIP) quand une nouvelle annonce correspond.
- Résultats en grille (FlashList, 2 colonnes) ou en liste, toggle animé.

### 3.7 Commentaires

- Sur profils, annonces et sujets du forum.
- Fils de discussion à **2 niveaux** (commentaire → réponses), likes, mention `@pseudo`, édition (15 min), suppression.
- Notation 1–5 ★ uniquement après une collaboration confirmée (anti‑faux avis).
- Optimistic update avec React Query, animation d'apparition (Moti).
- **Signalement** + filtre de mots interdits (liste JSON) + modération admin.

### 3.8 Messagerie (bonus)

Conversations 1‑to‑1 mockées en JSON, bulles néon, indicateur « vu », envoi d'images. Blocage d'utilisateur.

### 3.9 Back‑office admin (dans l'app, rôle `admin`)

Modération des annonces `pending`, gestion des signalements, validation des profils vérifiés, bannissement, statistiques simples.

---

## 4. Stockage JSON (phase 1)

### 4.1 Approche

Deux modes, sélectionnables via `EXPO_PUBLIC_DATA_MODE` :

1. **`local`** : les fichiers JSON de `src/data/seed/` sont chargés au premier lancement puis persistés/modifiés dans **MMKV** (fonctionne hors‑ligne, idéal pour la démo).
2. **`server`** : un **json-server** (`npx json-server db.json --port 3001`) sert `server/db.json` comme une API REST (GET/POST/PATCH/DELETE, pagination `_page`/`_limit`, filtres `?region=...`). Upload d'images mock dans `server/uploads/`.

Toute l'app passe par une **couche repository** :

```
src/data/
  repositories/
    types.ts            // interfaces IUserRepo, IListingRepo, ...
    local/              // implémentation MMKV + JSON
    http/               // implémentation json-server (fetch)
    index.ts            // choisit l'implémentation selon DATA_MODE
  seed/
    users.json
    profiles.json
    listings.json
    categories.json
    forumSections.json
    threads.json
    comments.json
    conversations.json
    messages.json
    reports.json
    vipPlans.json
  geo/
    regions.json         // code, nom, path SVG, centroid
    departments.json     // code, nom, regionCode, path SVG, centroid
    cities.json          // nom, codePostal, departmentCode, lat, lng
```

→ Plus tard, ajouter `repositories/supabase/` sans toucher aux écrans.

### 4.2 Schémas (TypeScript + zod dans `src/types/`)

```ts
type Role = 'model' | 'pro' | 'member' | 'admin';
type VipTier = 'FREE' | 'VIP' | 'VIP_GOLD';

interface User {
  id: string; email: string; passwordHash: string; // mock
  username: string; role: Role; birthDate: string; // ISO, >= 18 ans
  avatarUrl?: string; createdAt: string;
  vip: { tier: VipTier; since?: string; expiresAt?: string };
  verified: boolean; banned: boolean;
  consents: { terms: boolean; sensitiveData: boolean; marketing: boolean; date: string };
}

interface ModelProfile {
  userId: string; displayName: string; bio: string;
  photos: { id: string; url: string; blurhash?: string; order: number }[];
  location: { region: string; department: string; city: string; lat: number; lng: number };
  age: number; heightCm?: number; hair?: string; eyes?: string;
  origin?: string[];          // auto-déclaré, facultatif, nécessite consents.sensitiveData
  specialties: string[]; style: string[]; languages: string[];
  experience: 'beginner' | 'intermediate' | 'pro';
  rates?: { label: string; amount: number }[];
  rating: { avg: number; count: number }; views: number; featuredUntil?: string;
}

interface Listing {
  id: string; authorId: string;
  type: 'casting' | 'paid_shoot' | 'tfp' | 'event' | 'photographer_wanted';
  title: string; description: string; photos: string[];
  location: { region: string; department: string; city: string; lat: number; lng: number };
  dates: { start: string; end?: string };
  pay?: { amount: number; currency: 'EUR'; unit: 'hour' | 'day' | 'project' };
  requirements: { ageMin?: number; ageMax?: number; specialties?: string[]; experience?: string };
  status: 'draft' | 'pending' | 'active' | 'expired' | 'rejected';
  boosted: boolean; views: number; applicationsCount: number;
  createdAt: string; expiresAt: string;
}

interface Comment {
  id: string; targetType: 'profile' | 'listing' | 'thread'; targetId: string;
  authorId: string; parentId?: string; body: string;
  likes: string[]; createdAt: string; editedAt?: string; hidden: boolean;
}

interface ForumThread {
  id: string; sectionId: string; authorId: string; title: string; body: string;
  pinned: boolean; locked: boolean; likes: string[]; repliesCount: number;
  createdAt: string; lastActivityAt: string;
}

interface Report {
  id: string; reporterId: string; targetType: string; targetId: string;
  reason: 'spam' | 'fake' | 'minor' | 'illegal' | 'harassment' | 'other';
  details?: string; status: 'open' | 'resolved' | 'dismissed'; createdAt: string;
}
```

### 4.3 Données de seed

Génère un script `scripts/seed.ts` (avec **@faker-js/faker** locale `fr`) qui produit ~60 profils, ~150 annonces réparties sur toute la France (coordonnées réalistes), 6 sections de forum, ~80 sujets, ~400 commentaires. Photos : placeholders (ex. `https://picsum.photos/seed/<id>/600/800`) — **aucune photo de vraie personne**.

---

## 5. Architecture des dossiers

```
app/                         # Expo Router
  _layout.tsx                # fonts, providers, thème, SunsetBackground
  (auth)/login.tsx, register.tsx, onboarding.tsx
  (tabs)/_layout.tsx         # AnimatedTabBar néon
  (tabs)/index.tsx           # Accueil
  (tabs)/search.tsx          # Recherche + filtres
  (tabs)/map.tsx             # Carte France
  (tabs)/forum/index.tsx
  (tabs)/profile.tsx         # Mon compte
  profile/[id].tsx
  listing/[id].tsx
  listing/create.tsx         # stepper
  forum/[sectionId]/index.tsx
  forum/thread/[id].tsx
  vip.tsx                    # VIP Store
  messages/index.tsx, messages/[id].tsx
  admin/index.tsx
  settings.tsx, legal/cgu.tsx, legal/privacy.tsx
src/
  components/ui/ ...         # design system Vice City
  components/map/            # FranceMap, RegionMap, ListingMarker, ClusterMarker
  components/search/         # SearchBar, FilterSheet, ResultCounter, SavedSearches
  components/comments/       # CommentList, CommentItem, CommentInput
  features/                  # hooks métier (useListings, useSearch, useVip, ...)
  data/                      # repositories + seed + geo
  stores/                    # zustand (auth, filters, settings)
  theme/                     # colors, gradients, typography, spacing
  lib/                       # fuse, validators (zod), formatters (prix GTA), geo utils (distance haversine)
  i18n/
server/db.json               # pour json-server
scripts/seed.ts
```

---

## 6. Légal, sécurité et modération (obligatoire)

- **18+ uniquement** : contrôle à l'inscription, CGU explicites, bouton de signalement « personne mineure » traité en priorité.
- **Contenu non explicite** : pas de nudité, pas de contenu sexuel, pas d'offres de services sexuels ni d'« escorting » — interdit par les CGU, détecté par la liste de mots interdits + modération. (C'est aussi une exigence des règles **App Store** et **Google Play**, sinon l'app sera refusée.)
- **RGPD** : l'origine ethnique est une **donnée sensible (art. 9 RGPD)** → champ facultatif, auto‑déclaré, consentement explicite et séparé, retrait possible à tout moment, jamais affiché sans accord, jamais utilisé pour exclure dans les annonces de façon discriminatoire (rappel légal à l'éditeur d'annonce). Écran « Mes données » : export JSON + suppression du compte.
- Pages **CGU**, **Politique de confidentialité**, **Mentions légales**.
- Mots de passe hashés même en mock (`bcryptjs`), token dans `expo-secure-store`.
- Validation zod de toutes les entrées, limitation du nombre d'annonces/commentaires par minute (anti‑spam).
- Blocage et signalement d'utilisateurs, masquage automatique d'un contenu après N signalements en attente de modération.

---

## 7. Performance et accessibilité

- FlashList partout, `expo-image` avec blurhash, pagination infinie (20 éléments).
- Animations sur le UI thread (worklets Reanimated), 60 fps cible.
- Respect de `AccessibilityInfo.isReduceMotionEnabled` : couper le fond animé et les effets lourds.
- Contrastes suffisants sur fond sombre, `accessibilityLabel` sur tous les boutons, tailles de police dynamiques.
- Mode « effets réduits » dans les réglages pour les téléphones d'entrée de gamme.

---

## 8. Plan de livraison (à suivre dans l'ordre)

1. Init Expo + TypeScript + Expo Router + fonts + thème Vice City + `SunsetBackground` + `AnimatedTabBar`.
2. Couche data JSON (types, zod, repositories local/http, seed, json-server).
3. Auth mock + onboarding + vérification 18+ + consentements.
4. Accueil + catégories + cartes profils + écran profil (shared element transition).
5. Annonces : liste, détail, création multi‑étapes, « ANNONCE PUBLIÉE ! ».
6. Moteur de recherche + filtres avancés + compteur animé + recherches sauvegardées.
7. Carte France SVG (régions → départements) + carte détaillée avec clusters + synchro filtres.
8. Commentaires + notations + signalements.
9. Forum (sections, sujets, réponses).
10. VIP Store + gardes VIP + mises en avant.
11. Messagerie, admin/modération.
12. Tests (Jest + Testing Library), accessibilité, optimisation, config EAS Build.

À chaque étape : code complet et fonctionnel, pas de pseudo‑code, commentaires concis, et un court README expliquant comment lancer (`npx expo start`, `npm run seed`, `npm run api` pour json-server).

---

## 9. Critères d'acceptation

- L'app démarre sur Android et iOS (Expo Go ou dev build) sans erreur.
- Toutes les données proviennent des fichiers JSON via les repositories ; créer une annonce ou un commentaire persiste après redémarrage (mode `local`) ou dans `db.json` (mode `server`).
- La carte permet de passer France → région → département → annonces, et la sélection filtre la recherche.
- La recherche avancée combine au moins 10 filtres et met à jour le compteur en temps réel.
- Les fonctions VIP sont bloquées pour les comptes FREE avec un écran d'upsell.
- Le style Vice City (Pricedown, néons, sunset) est cohérent sur tous les écrans.
- Les règles 18+, RGPD et modération du § 6 sont implémentées.
