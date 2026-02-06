# Table-technique
Consultation dossier technique
Application de Détection de Mains - Navigation Interactive
Description
Application web utilisant MediaPipe Hands pour détecter les gestes de la main et interagir avec des images.
Fonctionnalités
1. Navigation par zones

Geste: Pouce + Index de la main droite qui se touchent (pinch)
Action: Lorsque le geste est détecté dans une zone définie, l'image change selon la configuration
6 zones rectangulaires par image

2. Zoom interactif

Geste: Les deux mains avec pouces et index qui se touchent simultanément
Action:

Écarter les mains = zoom avant (max 5x)
Rapprocher les mains = zoom arrière (min 1x)


Les zones ne sont pas actives pendant le zoom

Configuration (config.json)
json{
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
Structure des zones

pt1: Point supérieur gauche (x, y)
pt2: Point inférieur droit (x, y)
targetImage: Nom du fichier image de destination

Installation

Placez vos images PNG dans le même dossier que index.html
Configurez config.json avec vos images et zones
Ouvrez index.html dans un navigateur moderne (Chrome, Edge, Firefox)

Prérequis

Navigateur web avec support WebRTC
Webcam connectée
Connexion Internet (pour charger MediaPipe depuis CDN)
Images au format PNG

Utilisation

Autoriser l'accès à la webcam
La première image (1.png) s'affiche automatiquement
Utiliser les gestes pour naviguer ou zoomer

Interface

Vidéo en haut à droite: Flux webcam avec visualisation des mains
Panneau en haut à gauche: Informations système

État du système
Image actuelle
Geste détecté
Niveau de zoom


Zones rouges en pointillé: Zones interactives (debug)

Bibliothèques utilisées

MediaPipe Hands (Google): Détection et tracking des mains en temps réel

Haute performance
Précision excellente
Support multi-mains (jusqu'à 2)
21 points de repère par main



Paramètres MediaPipe
javascriptmaxNumHands: 2                    // Maximum 2 mains
modelComplexity: 1                // Bon compromis vitesse/précision
minDetectionConfidence: 0.7       // Confiance détection
minTrackingConfidence: 0.7        // Confiance tracking
Seuils de détection

Distance pinch: < 0.05 (distance normalisée entre pouce et index)
Zoom min: 1.0x
Zoom max: 5.0x

Personnalisation
Modifier les zones
Éditez config.json pour ajuster les coordonnées des zones
Changer les seuils
Dans le code JavaScript, modifiez:

distance < 0.05 (ligne 169) pour la sensibilité du pinch
Math.min(5.0, ...) (ligne 246) pour le zoom maximum

Masquer les zones debug
Commentez ou supprimez l'appel à drawDebugZones() (ligne 147)
Dépannage
La webcam ne s'active pas

Vérifier les permissions du navigateur
Tester dans Chrome/Edge (meilleur support WebRTC)

Les gestes ne sont pas détectés

Améliorer l'éclairage
Se rapprocher de la caméra
Éviter les arrière-plans complexes

Images non affichées

Vérifier que les fichiers PNG existent
Vérifier les noms dans config.json
Utiliser la console du navigateur (F12)

Performance

MediaPipe Hands fonctionne à ~30 FPS sur hardware moderne
Latence très faible (<50ms)
Optimisé pour détection en temps réel

Compatibilité navigateurs
✅ Chrome 90+
✅ Edge 90+
✅ Firefox 88+
✅ Safari 14+ (support limité)
Licence
Projet d'exemple utilisant MediaPipe (Apache 2.0)
