# Elomboua & Azibe — 12 décembre 2026, Maroua

Site de mariage « digne d'une agence » : une invitation animée, bilingue **français / anglais**,
un formulaire de confirmation (RSVP) et un **espace organisateurs complet** (tableau de bord,
gestion des invités, CMS pour modifier chaque texte et chaque photo).

Palette extraite du bouquet fourni (`assets/couleur.jpeg`) : terracotta `#A8561F`,
orange brûlé `#E8802F`, sable `#B49A79`, doré `#BE8C1E`, ivoire `#FBF7F2`.

---

## 1. Démarrage rapide

```bash
npm install          # ou pnpm install
npm run dev          # http://localhost:3000
```

Mise en production :

```bash
npm run build
npm run start        # http://localhost:3000
```

> `npm run build` doit passer sans erreur : c'est le contrôle le plus fiable avant publication.

---

## 2. Espace organisateurs — `/admin`

| | |
| --- | --- |
| URL | `/admin` (redirige vers `/admin/login` si non connecté) |
| Mot de passe par défaut | `elomboua2026` |
| Défini au premier accès par | la variable `ADMIN_PASSWORD` (voir `.env.example`) |

⚠️ **Changez le mot de passe dès la première connexion** : `/admin` → *Réglages*.
Il est stocké **haché avec scrypt** dans `data/admin.json` — jamais en clair.
8 tentatives échouées bloquent la connexion pendant 10 minutes.

Une fois connecté :

- **Tableau de bord** — nombre de réponses, confirmés / en attente / indisponibles,
  jours restants, dernières réponses.
- **Invités** — recherche, filtre par statut, ajout manuel, changement de statut,
  suppression (avec confirmation), **export CSV** compatible Excel
  (UTF-8 BOM + séparateur virgule).
- **Contenu du site** — le CMS (voir plus bas).
- **Réglages** — changement de mot de passe (invalide les sessions en cours),
  état des fichiers de données, note de confidentialité.

---

## 3. Le CMS : modifier chaque élément du site

`/admin/content` est organisé en onglets, calqués sur les sections du site :

| Onglet | Ce que vous modifiez |
| --- | --- |
| Identité & date | prénoms, familles, monogramme, hashtag, date/heure, ville, citation, SEO |
| Accueil & bandeau | bandeau d'annonce (activé/désactivé), sur-titre, boutons, photos de fond du diaporama |
| Compte à rebours | titre, sous-titre, message du jour J |
| Notre histoire | sur-titre, introduction et **étapes** (année, titre, texte, photo) |
| Programme | introduction et **temps forts** (heures, lieu, adresse, dress code, photo, icône) |
| Lieux | adresses, notes pratiques, photo, recherche Google Maps |
| Galerie | photos, légendes et **textes alternatifs** (accessibilité) |
| Infos & cadeaux | cartes pratiques (8 icônes disponibles) et options cadeaux |
| Réponses (RSVP) | tous les textes du formulaire, date limite, messages de succès/erreur |
| FAQ | questions et réponses |
| Familles | message de chaque famille |
| Pied de page | message final, signature, contacts (téléphone/e-mail) |
| Apparence | les 6 couleurs du site |
| Ordre des sections | afficher / masquer et **réordonner** les blocs de la page d'accueil |

**Bon à savoir**

- Tous les champs de texte existent **en français et en anglais** (deux champs côte à côte).
- Les **listes** (étapes, événements, lieux, photos, FAQ…) se réordonnent avec les flèches,
  se dupliquent et se suppriment.
- Les **images** se téléversent directement (JPG, PNG, WEBP, AVIF, SVG — 8 Mo max).
  Elles sont stockées dans `data/uploads/` et servies par `/uploads/...`.
  Les photos livrées d'origine sont dans `public/images/`.
- Rien n'est publié avant le bouton **Enregistrer**. La barre du bas indique s'il reste
  des modifications non enregistrées.

---

## 4. Bilingue FR / EN

- La langue est mémorisée dans un cookie (`ea-lang`) : **FR par défaut**, bascule `FR | EN`
  dans l'en-tête (et dans le menu mobile).
- Le contenu est rendu côté serveur dans la langue choisie → `<html lang="fr">` correct,
  SEO propre, aucun flash de traduction.
- Le formulaire RSVP enregistre la langue de l'invité (visible dans l'export CSV).

---

## 5. Accessibilité & inclusivité

Le site vise **WCAG 2.2 niveau AA** :

- Contrastes vérifiés (texte principal 15:1, liens 6:5:1, boutons ≥ 4,9:1).
- **Lien d'évitement** (« Aller au contenu principal »), repères ARIA, titres hiérarchisés.
- **Navigation clavier complète** avec anneau de focus visible sur fond clair *et* sombre.
- **Mouvement réduit** : respect de `prefers-reduced-motion` **et** interrupteur « Réduire les
  animations » dans l'en-tête (mémorisé).
- **Aucune animation n'est indispensable** : le contenu est visible même sans JavaScript
  (les révélations au scroll sont une amélioration progressive).
- **Galerie** : `<dialog>` natif (piège de focus + `Échap`), flèches clavier, compteur,
  texte alternatif bilingue.
- **FAQ** en `<details>/<summary>` natif — utilisable sans JS et par les lecteurs d'écran.
- Formulaires avec `label`, `aria-describedby`, messages d'erreur `role="alert"`.
- **Inclusif dans le fond** : aucune question sur le genre, aucune mention « M. / Mme »,
  formulation épicène, enfants explicitement bienvenus, accessibilité PMR documentée,
  allergies et régimes pris en compte, annonces en français / anglais / foufouldé,
  refus clair de toute pression sur les cadeaux, engagement « pas de harcèlement » sur la piste.
- **Vie privée** : polices auto-hébergées (aucune requête Google), cartes chargées
  **au clic** seulement, aucun cookie de suivi, `robots.txt` interdit `/admin`.
- `prefers-contrast: more` renforce les contrastes.

---

## 6. Animations

- Titre du hero : apparition lettre par lettre (CSS, décalée), diaporama **Ken Burns**
  avec fondu long, pétales flottants.
- Parallaxe du hero calculée en `requestAnimationFrame` (déterministe, neutre au repos)
  et barre de progression de lecture.
- Révélations au défilement par `IntersectionObserver` (fondu + remontée + flou) avec
  délais en cascade, réinitialisées à chaque nouveau bloc.
- Défilement fluide **Lenis**, coupé automatiquement si mouvement réduit.
- Micro-interactions : reflet doré sur les boutons, cartes qui se soulèvent,
  zoom lent des images au survol, soulignés animés, marquee.
- **Toutes** désactivables d'un clic, sans perte d'information.

---

## 7. Structure du projet

```
app/
  layout.tsx                 polices, thème, métadonnées, confort visuel
  page.tsx                   page d'accueil (compose les sections)
  globals.css                design system (couleurs, typo, animations)
  fonts.css                  @font-face auto-hébergées (généré)
  ical/route.ts              fichier calendrier .ics (3 rendez-vous)
  uploads/[...path]/route.ts sert les images téléversées
  api/rsvp/route.ts          formulaire public
  api/admin/*                login, logout, contenu, invités, upload, mot de passe
  admin/login                connexion
  admin/(authed)/*           tableau de bord, invités, CMS, réglages
components/
  site/                      sections publiques animées
  admin/                     coquille, champs, éditeur CMS, gestion invités
  providers/                 Lenis + préférence de mouvement
lib/
  content.ts store.ts        lecture / écriture JSON atomique + verrou
  rsvp.ts auth.ts            invités, sessions signées (HMAC)
  i18n.ts types.ts           dictionnaire FR/EN, types
  cms-schema.ts              description du CMS (champs, listes, onglets)
data/
  content.json               tout le contenu du site (source de vérité)
  rsvp.json                  réponses des invités
  admin.json                 identifiants (hachés) + secret de session
  uploads/                   photos téléversées
public/
  images/                    photos d'illustration + CREDITS.md
  fonts/                     polices auto-hébergées
scripts/fetch-fonts.mjs      régénère app/fonts.css et public/fonts
```

---

## 8. Données & sauvegarde

Tout est en JSON, sans base de données :

- **Sauvegarde** = copier le dossier `data/` (contenu, réponses, identifiants et photos).
- Chaque enregistrement crée une copie `*.json.bak` de la version précédente.
- Écriture **atomique** (fichier temporaire + `rename`) et verrou pour éviter
  toute corruption en cas d'écritures simultanées.

---

## 9. Déploiement

| Cible | Verdict |
| --- | --- |
| VPS / Node (Railway, Render, Fly.io, o2switch…) | ✅ **recommandé** : le disque persiste, `data/` fonctionne tel quel |
| Vercel / Netlify (serverless) | ⚠️ le système de fichiers est **éphémère** : les réponses et images téléversées seraient perdues |

Sur un hébergement serverless, il faut déplacer `data/` vers une base (Supabase, Turso,
Postgres) et les images vers un stockage objet (S3, Cloudinary). L'architecture est prête :
seuls `lib/store.ts`, `lib/rsvp.ts` et `app/api/admin/upload/route.ts` sont concernés.

Avant publication : changez `ADMIN_PASSWORD`, définissez `NEXT_PUBLIC_SITE_URL`,
et remplacez les photos d'illustration par les vôtres. Le mot de passe par défaut
`elomboua2026` s'applique tant que `data/admin.json` n'existe pas : renseignez
`ADMIN_PASSWORD` dans `.env.local` **avant le premier lancement** si vous voulez
un autre mot de passe initial.

---

## 10. Crédits

- Photos d'illustration : [Unsplash](https://unsplash.com/license) — détail dans
  `public/images/CREDITS.md`. Le bouquet de la palette provient de vos assets.
- Polices : Cormorant Garamond, Inter, Pinyon Script — SIL Open Font License 1.1.
