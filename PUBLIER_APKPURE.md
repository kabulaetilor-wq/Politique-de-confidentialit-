# Publier Gem Rush 3D sur APKPure — guide pas à pas

> Les étapes de la console APKPure (§5) viennent de la page d'aide officielle d'APKPure, consultée le 28/10/2026 ;
> l'interface peut changer. Les points qu'APKPure ne documente pas (taille des captures, règles exactes de refus) sont marqués « à vérifier ».

## 0. Avant de commencer — 5 choses à faire absolument

| # | À faire | Pourquoi |
|---|---------|----------|
| 1 | Remplacer les **ID de test AdMob** par les tiens (`AndroidManifest.xml` : `APPLICATION_ID`, `MainActivity.kt` : `BANNER_ID`) | Avec les ID de test, **tu ne gagnes aucun argent**. Avec de vrais ID, **ne clique jamais toi-même** sur tes pubs (risque de bannissement AdMob). |
| 2 | Créer **ta propre clé de signature** (§1) | L'APK actuel est signé avec la clé *debug*. APKPure exige que les **mises à jour soient signées avec la même clé** que la première version : il faut la garder pour toujours. |
| 3 | Remplir la **politique de confidentialité** (§3) et l'héberger | Obligatoire dès qu'il y a de la publicité. |
| 4 | Vérifier les **droits des modèles** (Banny, etc.) | Si tu publies, tu dois avoir le droit d'utiliser les modèles fournis par toi. C'est ta responsabilité. |
| 5 | Tester l'APK sur un vrai téléphone | Le jeu n'a été vérifié que par compilation et rendus logiciels : jamais sur appareil. |

## 1. Créer ta clé de signature (une seule fois)

Sur ton ordinateur (Java installé) :

```bash
keytool -genkeypair -v -keystore gemrush-release.jks -alias gemrush -keyalg RSA -keysize 2048 -validity 10000
```

- Choisis un mot de passe fort pour le keystore **et** pour la clé, et note-les.
- **Sauvegarde `gemrush-release.jks` et les mots de passe à plusieurs endroits** (clé USB, gestionnaire de mots de passe). Si tu la perds, tu ne pourras plus mettre à jour l'application sous le même nom de paquet.
- **Ne mets jamais ce fichier dans le dépôt GitHub public.**

### Brancher la clé sur GitHub Actions (le dépôt est déjà prêt)

1. Transforme le fichier en texte : `base64 -w0 gemrush-release.jks` (sur Mac : `base64 -i gemrush-release.jks`).
2. Sur GitHub : dépôt › **Settings › Secrets and variables › Actions › New repository secret**. Crée 4 secrets :
   - `KEYSTORE_BASE64` : le texte obtenu à l'étape 1
   - `KEYSTORE_PASSWORD` : mot de passe du keystore
   - `KEY_ALIAS` : `gemrush`
   - `KEY_PASSWORD` : mot de passe de la clé
3. Lance le workflow : onglet **Actions › Build Android APK › Run workflow**, ou pousse un tag : `git tag v1.0 && git push origin v1.0`.
4. L'APK signé est dans les **artifacts** du run et, pour un tag, dans **Releases**.

Sans ces secrets, le workflow continue de produire un APK signé avec la clé debug (pratique pour tester, **mauvais pour publier**).

## 2. Préparer les éléments de la fiche

| Élément | Fichier / valeur |
|---|---|
| Nom | Gem Rush 3D |
| Nom de paquet | `com.gemrush.game` (ne pourra plus changer) |
| Icône | `store/icon-512.png` (déjà incluse dans l'APK : `res/mipmap-*`) |
| Image de présentation | `store/feature-graphic-1024x500.png` |
| Captures d'écran | 4 à 8 captures **de ton téléphone** (portrait). Formats exacts : à vérifier dans la console |
| Catégorie | Jeux › Arcade (ou Action) |
| Version | `versionName` 1.0 / `versionCode` 1 dans `app/build.gradle.kts` |
| Politique de confidentialité | URL GitHub Pages (§3) |

**Description courte (80 car.)** : « Cours sur les trains, saute, glisse et ramasse des gemmes dans un runner 3D ! »

**Description longue** :
> Gem Rush 3D est un jeu de course sans fin en 3D. Esquive les barrières, saute sur les toits des trains, glisse sous les poutres et ramasse un maximum de pièces avant que le garde et son chien ne te rattrapent !
> • 5 univers : ville, désert, neige, jungle, néon • Plus de 100 décors • Trains, métros, TGV et wagons variés • Personnages et tenues à débloquer • Planches volantes et bonus (aimant, jetpack, pogo…) • Missions, mot du jour, événements
> Le jeu contient de la publicité (bannière).

## 3. Publier la politique de confidentialité sur GitHub (dépôt public)

Fichier prêt : `docs/index.html` (FR + EN, thème clair/sombre, logo).

1. **Remplace** dans `docs/index.html` : `[VOTRE NOM OU PSEUDO]`, `[VOTRE EMAIL]`, `[YOUR NAME OR NICKNAME]`, `[YOUR EMAIL]` (Ctrl+F). Mets une adresse que tu es prêt à rendre **publique**.
2. Pousse le projet sur un dépôt **public** (si ce n'est pas fait) : `git add . && git commit -m "Politique de confidentialité" && git push`.
3. Sur GitHub : dépôt › **Settings › Pages** › *Build and deployment* › Source : **Deploy from a branch** › Branch : **main**, dossier **/docs** › **Save**.
4. Après 1 à 2 minutes, la page est en ligne à l'adresse :
   `https://TON-PSEUDO.github.io/NOM-DU-DEPOT/`
5. **Ouvre l'URL** pour vérifier qu'elle s'affiche, puis copie-la dans la fiche APKPure (et dans AdMob › Applications › Informations sur l'application).

Si tu ajoutes plus tard des achats intégrés, vidéos récompensées ou un classement en ligne, **mets la politique à jour**.

## 4. Compte APKPure (gratuit)

1. Crée un **compte utilisateur APKPure** (apkpure.com › Sign up).
2. Va sur **https://developer.apkpure.com/** et clique **REGISTER NOW**.
3. Remplis le profil développeur : nom de développeur, e-mail, site web (ton GitHub convient), bio facultative. Entre le **code de vérification** reçu par e-mail puis clique **REGISTER**.

Différence compte utilisateur / compte développeur : seul le compte développeur permet d'ajouter des applications.

## 5. Envoyer le jeu

1. Dans la console : **Add APPLICATION** → saisis le **nom** (Gem Rush 3D) et le **nom de paquet** `com.gemrush.game`.
   - (Si l'app existe déjà sur APKPure, utilise **CLAIM APPS** : entrée du nom de paquet, puis code de vérification reçu par e-mail ; APKPure examine la demande.)
2. Remplis la fiche : icône, descriptions, captures, catégorie, URL de la politique de confidentialité.
3. **MANAGE VERSIONS › SELECT FILES** : choisis `gem-rush-3d.apk` (le fichier **signé avec ta clé**, téléchargé depuis Actions/Releases). Taille max indiquée : 2 Go ; formats acceptés : APK (ou XAPK). La version et l'icône sont lues automatiquement.
4. Clique **Submit for Review**.
5. Délai annoncé : **généralement 24 à 48 h**. APKPure vérifie l'absence de virus et le respect de ses règles de contenu. Après approbation, l'application est publiée dans le monde entier ; tu suis téléchargements et notes dans le tableau de bord.

## 6. Publier une mise à jour

1. Dans `app/build.gradle.kts`, **augmente `versionCode`** (2, 3, …) et `versionName` (1.1, …).
2. Pousse un nouveau tag (`v1.1`) : Actions produit l'APK signé avec **la même clé**.
3. Console APKPure › ton app › **MANAGE VERSIONS** › envoie le nouvel APK › **Submit for Review**.

## 7. Si l'APK est refusé

Raisons fréquentes (à vérifier avec le message de refus) : APK signé avec la clé debug ou différente de la première version ; `versionCode` pas plus grand ; application détectée comme suspecte (publicité agressive, permissions inutiles) ; contenu protégé par des droits d'auteur (modèles 3D, sons, noms de marques) ; fiche incomplète ou captures trompeuses ; politique de confidentialité inaccessible.
Contact APKPure pour les partenariats : business@apkpure.com (source : leur page d'aide).

## 8. Bon à savoir avant de monétiser

- **Consentement RGPD (Europe/Royaume-Uni)** : l'application **n'a pas encore** de fenêtre de consentement publicitaire (Google UMP). Si tu as des joueurs dans ces régions, c'est à ajouter avant d'activer les vraies pubs. Je peux le faire dans une prochaine étape.
- **Google Play** (alternative) : demande un compte à frais uniques, un fichier AAB, une politique de confidentialité et le formulaire « Sécurité des données ». Les modèles générés par code et l'AdMob s'y publient de la même façon.
- Garde une **copie de l'APK signé** de chaque version publiée.
