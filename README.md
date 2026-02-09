# Application de Détection de Mains - Navigation Interactive

## Description
Application web utilisant MediaPipe Hands pour détecter les gestes de la main et interagir avec des images.

## Fonctionnalités

### 1. Pointeur visuel (Main Droite)
- **Affichage**: Cercle vert qui suit l'index de la main droite
- **États**:
  - Vert : Main détectée en mouvement
  - Rouge : Geste d'index levé actif (index levé, autres doigts baissés)
- **Visibilité**: Actif uniquement avec une seule main droite, masqué en mode zoom

### 2. Navigation par zones
- **Geste**: Index levé, autres doigts baissés (👆 geste de pointage)
- **Action**: Lorsque le geste est détecté dans une zone définie, l'image change selon la configuration
- 6 zones rectangulaires par image
- Le pointeur devient rouge lors de l'activation
- **Anti-rebond**: Délai de 1 seconde entre les changements d'image pour éviter les sélections multiples

### 3. Zoom interactif + Translation
- **Geste**: Les deux mains avec pouces et index qui se touchent simultanément (pinch)
- **Actions**: 
  - **Zoom**: Écarter/rapprocher les mains (1x à 8x)
  - **Translation**: Déplacer les deux mains ensemble pour bouger l'image zoomée
    - Déplacement horizontal (axe X)
    - Déplacement vertical (axe Y)
    - Translation limitée à ±500px pour éviter de perdre l'image
- Les zones ne sont pas actives pendant le zoom
- La translation se réinitialise automatiquement au retour au zoom 1x

## Configuration (config.json)

```json
{
  "images": [
    {
      "id": 1,
      "file": "1.png",
      "zones": [
        {
          "pt1": { "x": 100, "y": 50 },
          "pt2": { "x": 200, "y": 100 },
          "targetImage": "2.png"
        }
        // ... 5 autres zones
      ]
    }
  ]
}
```

### Structure des zones
- **pt1**: Point supérieur gauche (x, y)
- **pt2**: Point inférieur droit (x, y)
- **targetImage**: Nom du fichier image de destination

## Installation

1. Placez vos images PNG dans le même dossier que index.html
2. Configurez config.json avec vos images et zones
3. Ouvrez index.html dans un navigateur moderne (Chrome, Edge, Firefox)

## Prérequis

- Navigateur web avec support WebRTC
- Webcam connectée
- Connexion Internet (pour charger MediaPipe depuis CDN)
- Images au format PNG

## Utilisation

1. Autoriser l'accès à la webcam
2. La première image (1.png) s'affiche automatiquement
3. Utiliser les gestes pour naviguer ou zoomer

### Gestes détaillés

#### Navigation simple (AMÉLIORÉ - Plus stable)
1. Montrer la **main droite** uniquement
2. Le pointeur vert suit votre index
3. **Lever l'index** en gardant les autres doigts baissés 👆 → le pointeur devient rouge
4. Placer le pointeur dans une zone → l'image change
5. **Délai de sécurité**: 1 seconde entre chaque changement d'image

#### Zoom et déplacement
1. Montrer les **deux mains**
2. Faire un **pinch avec chaque main** (pouce + index qui se touchent)
3. **Écarter les mains** = zoom avant (jusqu'à 8x)
4. **Rapprocher les mains** = zoom arrière
5. **Déplacer les deux mains ensemble** = translation de l'image
6. Relâcher le pinch pour sortir du mode zoom

### Interface

- **Vidéo en haut à droite**: Flux webcam avec visualisation des mains
- **Pointeur vert/rouge**: Position de l'index main droite
- **Panneau en haut à gauche**: Informations système
  - État du système
  - Image actuelle
  - Geste détecté
  - Niveau de zoom
  - Position de translation (en mode zoom)
- **Zones rouges en pointillé**: Zones interactives (debug)

## Bibliothèques utilisées

- **MediaPipe Hands** (Google): Détection et tracking des mains en temps réel
  - Haute performance
  - Précision excellente
  - Support multi-mains (jusqu'à 2)
  - 21 points de repère par main
  - Reconnaissance gauche/droite

## Paramètres MediaPipe

```javascript
selfieMode: true                  // Optimisé pour webcam frontale
maxNumHands: 2                    // Maximum 2 mains
modelComplexity: 1                // Bon compromis vitesse/précision
minDetectionConfidence: 0.5       // Confiance détection
minTrackingConfidence: 0.5        // Confiance tracking
```

## Seuils et limites

- **Distance pinch**: < 0.05 (distance normalisée entre pouce et index) - Pour le zoom uniquement
- **Geste index levé**: 
  - Index tendu (bout plus haut que l'articulation)
  - Majeur, annulaire, auriculaire pliés
  - Index à plus de 0.1 au-dessus du poignet
- **Zoom**: 1.0x (min) à 8.0x (max)
- **Translation**: ±500px sur chaque axe
- **Sensibilité translation**: x2 (mouvement amplifié pour meilleur contrôle)
- **Anti-rebond changement d'image**: 1 seconde entre chaque changement

## Points de repère utilisés

- **Landmark 0**: Poignet
- **Landmark 4**: Pouce (bout)
- **Landmark 6**: Index (articulation PIP)
- **Landmark 8**: Index (bout)
- **Landmark 10, 12**: Majeur (articulation, bout)
- **Landmark 14, 16**: Annulaire (articulation, bout)
- **Landmark 18, 20**: Auriculaire (articulation, bout)
- **Détection main**: Label 'Right' ou 'Left' fourni par MediaPipe

## Personnalisation

### Modifier le pointeur
Dans le CSS, section `#pointer`:
- `width` et `height`: Taille du pointeur
- `border-color`: Couleur normale (vert par défaut)
- `.active` : Couleur en mode pinch (rouge par défaut)

### Modifier les seuils
Dans le code JavaScript:
- `distance < 0.05` : Sensibilité du pinch (zoom)
- `Math.min(8.0, ...)` : Zoom maximum
- `maxTranslate = 500` : Limite de translation
- `imageChangeDelay = 1000` : Délai anti-rebond en millisecondes (1000 = 1 seconde)
- Position Y pour détection doigts baissés : Ajuster les comparaisons `middleTip.y > middlePip.y`

### Changer la sensibilité de translation
Modifier le multiplicateur:
```javascript
const deltaX = (centerX - lastCenterX) * window.innerWidth * 2; // Modifier le 2
const deltaY = (centerY - lastCenterY) * window.innerHeight * 2; // Modifier le 2
```

### Masquer les zones debug
Commentez l'appel à `drawDebugZones()` dans `loadImage()`

## Améliorations de stabilité

### Geste de sélection repensé
Le geste de sélection utilise maintenant **l'index levé** au lieu du pinch pour éviter:
- Les zooms intempestifs lors de la navigation
- Les confusions entre gestes de sélection et de zoom
- Les faux positifs dus aux mouvements de main

### Anti-rebond
- **Délai de 1 seconde** entre chaque changement d'image
- Évite les sélections multiples accidentelles
- Permet de traverser des zones sans déclencher plusieurs changements
- Configurable via `imageChangeDelay`

### Détection de zones améliorée
- Utilise les coordonnées d'écran directes (plus précis)
- Fallback sur chargement direct si l'image n'est pas dans la config
- Logs console pour le débogage
- Vérification de l'ID avant changement pour éviter les rechargements inutiles

## Dépannage

### Le pointeur ne s'affiche pas
- Vérifier que vous utilisez la **main droite**
- Améliorer l'éclairage
- Vérifier la console du navigateur (F12)

### Le geste d'index levé n'est pas détecté
- Assurez-vous que **seul l'index est levé**
- Les autres doigts (majeur, annulaire, auriculaire) doivent être **bien repliés**
- Levez l'index **bien au-dessus** du niveau des autres doigts
- Évitez les positions ambiguës (semi-pliées)

### Les images ne changent pas
- Vérifiez la console (F12) pour voir les logs de chargement
- Vérifiez que les fichiers PNG existent dans le même dossier
- Vérifiez que les noms dans config.json correspondent exactement aux fichiers
- Attendez 1 seconde entre chaque changement (anti-rebond)
- Vérifiez que l'ID dans config.json est correct

### La translation est trop sensible/pas assez
- Modifier le multiplicateur (actuellement x2) dans les calculs deltaX/deltaY
- Ajuster maxTranslate pour limiter le débordement

### Les gestes ne sont pas détectés
- Améliorer l'éclairage
- Se rapprocher de la caméra
- Éviter les arrière-plans complexes
- Vérifier que les doigts se touchent bien pour le pinch

### Images non affichées
- Vérifier que les fichiers PNG existent
- Vérifier les noms dans config.json
- Utiliser la console du navigateur (F12)

## Performance

- MediaPipe Hands fonctionne à ~30 FPS sur hardware moderne
- Latence très faible (<50ms)
- Optimisé pour détection en temps réel
- Le pointeur est mis à jour à chaque frame

## Compatibilité navigateurs

✅ Chrome 90+ (Recommandé)
✅ Edge 90+
✅ Firefox 88+
⚠️ Safari 14+ (support limité)

## Licence

Projet d'exemple utilisant MediaPipe (Apache 2.0)
