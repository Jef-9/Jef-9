# Zémidjan 🏙️

> L'annuaire mobile des services de Cotonou : trouver une entreprise, un service ou un artisan près de chez soi, en quelques appuis.

<!-- Ajoute ici une bannière ou un GIF de démonstration : docs/screenshots/demo.gif -->

## Pourquoi ce projet ?

À Cotonou, les services locaux se trouvent surtout par le bouche-à-oreille, et les petits prestataires manquent de visibilité. Zémidjan regroupe plusieurs domaines de besoin dans une seule application, avec un espace d'administration pour garder l'annuaire à jour.

## Captures d'écran

| Accueil | Détail d'une fiche | Profil | Administration |
|---|---|---|---|
| ![Accueil](docs/screenshots/accueil.png) | ![Détail](docs/screenshots/detail.png) | ![Profil](docs/screenshots/profil.png) | ![Admin](docs/screenshots/admin.png) |

## Fonctionnalités

**Côté utilisateur**
- Inscription et connexion par e-mail, ou avec Google ; réinitialisation du mot de passe
- Accueil avec carrousel « Nouveautés » à défilement automatique et carrousel de catégories
- Recherche par nom et filtre par catégorie
- Fiche détaillée avec **galerie de plusieurs photos** (zoom plein écran) et bouton d'appel
- Profil : changement de photo, de nom et de mot de passe
- Tiroir de paramètres : thème clair / sombre / automatique, notifications, à propos
- Alerte dans l'app quand une nouvelle fiche est ajoutée
- Animations (apparition en cascade, transitions de page) et navigation par barre inférieure

**Côté administrateur**
- Ajout, modification et suppression des fiches et des catégories
- Ajout de **plusieurs photos d'un coup** (jusqu'à 8 par fiche) et choix de la photo de couverture

## Technologies

| Couche | Choix |
|---|---|
| Application | Flutter (Dart), Material 3 |
| Authentification | Firebase Authentication (e-mail/mot de passe, Google Sign-In) |
| Données | Firebase Realtime Database (mise à jour en temps réel) |
| Sécurité | Règles Firebase : lecture réservée aux utilisateurs connectés, écriture réservée aux administrateurs |

## Architecture

```
lib/
├── main.dart            démarrage de Firebase et thème
├── models/              Category, Place
├── services/            DbService (base de données), AuthService (comptes)
├── state/               AppState : profil, thème, notifications, statut admin
├── screens/             accueil, explorer, profil, authentification, administration
└── widgets/             composants réutilisables (carrousels, galerie, animations, tiroir)
```

Les écrans passent par les services et jamais par Firebase directement : le jour où la base change, seuls les services et les modèles sont à réécrire. L'état global (`AppState`) est un `ChangeNotifier` : les écrans se reconstruisent quand le profil ou le thème change.

**Choix techniques à noter**
- Les fiches et leur galerie sont enregistrées **en une seule écriture atomique** (fiche et photos ensemble, ou rien).
- Les listes ne chargent que la photo de couverture ; la galerie complète n'est lue que sur l'écran de détail.
- Les préférences (thème, notifications, photo) suivent le compte d'un téléphone à l'autre.

## Lancer le projet

Prérequis : Flutter, un projet Firebase (offre gratuite Spark suffisante), un téléphone ou émulateur Android.

```bash
git clone https://github.com/Jef-9/zemidjan.git
cd zemidjan
flutter pub get
dart pub global activate flutterfire_cli
flutterfire configure        # génère lib/firebase_options.dart pour TON projet Firebase
flutter run
```

Configuration Firebase :
1. **Authentication** : activer les méthodes *Google* et *E-mail/Mot de passe*.
2. **Realtime Database** : créer la base, puis coller le contenu de `database.rules.json` dans l'onglet *Règles*.
3. **Android** : ajouter l'empreinte SHA-1 de la clé de debug (`cd android && ./gradlew signingReport`).
4. **Devenir administrateur** : créer dans la base le nœud `admins/<UID de ton compte>` avec la valeur `true`.

## Limites connues et pistes d'amélioration

- Les photos sont stockées en base64 dans la base (150 Ko max, 8 par fiche) pour rester sur l'offre gratuite. Pour un annuaire plus grand, passer à un vrai stockage de fichiers.
- Les notifications fonctionnent app ouverte ; les notifications push (app fermée) demanderaient Firebase Cloud Messaging.
- La recherche par nom est faite côté application : adaptée à quelques centaines de fiches.
- Idées : favoris, carte et itinéraire vers le prestataire, avis des utilisateurs.

## Auteur

**Waldo YEMADJE** : étudiant en Master  Génie Logiciel, IFRI-UAC (Bénin).
 · corajef84@gmail.com
