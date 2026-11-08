# HÔPITAUX MANSSAH

Application de gestion des patients et du parcours médical de la campagne MANSSAH / REDEO.

Cet export reprend la version en ligne de recette privée **12**, publiée le 7 novembre 2026. Le commit source exact est indiqué dans `SOURCE_VERSION.json`.

## Fonctionnalités

- Enregistrement, recherche, pagination, références et QR codes patients.
- Poids/taille et IMC, glycémie, tension, bucco-dentaire et évaluation neurologique facultative.
- Comptes intendant, infirmier, généraliste, spécialiste, superviseur et administrateur.
- Orientation, motif prérempli, révocation historisée et plafond quotidien par spécialiste (50 par défaut).
- Enquêtes néphrologiques, examen neurologique structuré, bilan obligatoire avant clôture.
- Synthèse clinique locale factuelle ; aucune API de diagnostic IA externe.
- Statistiques, bilans métier, rapports PDF, CSV et sauvegardes chiffrées.

## Contenu et limites de cet export

Le dépôt contient le code source, les ressources graphiques, le schéma, les migrations et les tests. Il ne contient aucune base patients, session, sauvegarde de données, configuration secrète ou dépendance installée.

La migration `0010_private_load_fixtures.sql` est volontairement neutralisée dans cet export : les comptes de charge à mots de passe prédéfinis de la recette privée ne sont pas créés lors d'une installation neuve. Son numéro est conservé pour respecter la séquence des migrations. Les autres migrations et fonctionnalités applicatives sont conservées.

Il s'agit de la version **Vinext / Cloudflare Workers / D1 (SQLite)** de l'application en ligne. Ce n'est pas le paquet Next.js / PostgreSQL de l'installation intranet sur Mac. Copier le code sur GitHub ne transfère ni la base, ni les comptes existants, ni l'hébergement.

## Développement

Prérequis : Node.js 22.13 ou ultérieur et pnpm correspondant à la propriété `packageManager` de `package.json`.

```bash
pnpm install --frozen-lockfile
pnpm dev
```

La configuration de développement définit la liaison D1 `DB` et le runtime Cloudflare dans `vite.config.ts`. Une base vide doit recevoir les migrations de `drizzle/` avant de pouvoir traiter des dossiers.

```bash
pnpm build
pnpm start
```

Le runtime Cloudflare et la liaison `DB` restent nécessaires en production. La configuration `.openai/hosting.json` identifie le site source ; toute publication vers un nouvel environnement nécessite une configuration propre à cet environnement.

## Vérification sans base de production

Ces vérifications utilisent des données fictives dans des bases isolées :

```bash
node node_modules/typescript/bin/tsc --noEmit
node scripts/check-specialist-workflow.mjs
node scripts/check-nephrology.mjs
node scripts/check-neurology.mjs
node scripts/check-specialist-clinical-statistics.mjs
node scripts/check-backup.mjs
```

Le scénario de test HTTP complet utilise également une base D1 isolée, après compilation :

```bash
node scripts/check-built-patient.mjs
```

Les scripts de charge `recette` et `hosted-load-hour` sont destinés aux essais autorisés et ne doivent pas être lancés sur une base de campagne réelle. Pour les détails du socle technique, consulter `docs/TECHNICAL_STARTER.md`.
