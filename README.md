# Table-technique
Consultation dossier technique
# Application de Détection de Mains - Navigation Interactive

## Description
Application web utilisant MediaPipe Hands pour détecter les gestes de la main et interagir avec des images.

## Fonctionnalités

### 1. Pointeur visuel (Main Droite)
- **Affichage**: Cercle vert qui suit l'index de la main droite
- **États**:
  - Vert : Main détectée en mouvement
  - Rouge : Geste pinch actif (pouce + index qui se touchent)
- **Visibilité**: Actif uniquement avec une seule main droite, masqué en mode zoom

### 2. Navigation par zones
- **Geste**: Pouce + Index de la main droite qui se touchent (pinch)
- **Action**: Lorsque le geste est détecté dans une zone définie, l'image change selon la configuration
- 6 zones rectangulaires par image
- Le pointeur devient rouge lors de l'activation

### 3. Zoom interactif + Translation
- **Geste**: Les deux mains avec pouces et index qui se touchent simultanément
- **Actions**: 
  - **Zoom**: Écarter/rapprocher les mains (1x à 5x)
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

#### Navigation simple
1. Montrer la **main droite** uniquement
2. Le pointeur vert suit votre index
3. Faire un **pinch** (pouce + index) → le pointeur devient rouge
4. Placer le pointeur dans une zone → l'image change

#### Zoom et déplacement
1. Montrer les **deux mains**
2. Faire un **pinch avec chaque main** (pouce + index)
3. **Écarter les mains** = zoom avant
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

- **Distance pinch**: < 0.05 (distance normalisée entre pouce et index)
- **Zoom**: 1.0x (min) à 5.0x (max)
- **Translation**: ±500px sur chaque axe
- **Sensibilité translation**: x2 (mouvement amplifié pour meilleur contrôle)

## Points de repère utilisés

- **Landmark 4**: Pouce (bout)
- **Landmark 8**: Index (bout)
- **Détection main**: Label 'Right' ou 'Left' fourni par MediaPipe

## Personnalisation

### Modifier le pointeur
Dans le CSS, section `#pointer`:
- `width` et `height`: Taille du pointeur
- `border-color`: Couleur normale (vert par défaut)
- `.active` : Couleur en mode pinch (rouge par défaut)

### Modifier les seuils
Dans le code JavaScript:
- `distance < 0.05` : Sensibilité du pinch
- `Math.min(5.0, ...)` : Zoom maximum
- `maxTranslate = 500` : Limite de translation

### Changer la sensibilité de translation
Modifier le multiplicateur:
```javascript
const deltaX = (centerX - lastCenterX) * window.innerWidth * 2; // Modifier le 2
const deltaY = (centerY - lastCenterY) * window.innerHeight * 2; // Modifier le 2
```

### Masquer les zones debug
Commentez l'appel à `drawDebugZones()` dans `loadImage()`

## Dépannage

### Le pointeur ne s'affiche pas
- Vérifier que vous utilisez la **main droite**
- Améliorer l'éclairage
- Vérifier la console du navigateur (F12)

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
