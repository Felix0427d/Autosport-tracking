# Autosport tracking

## Idées générales
L'idée du projet avant d'être un projet potentiellement fonctionnel, etst de retravailler tout le bagage technique acquis à l'ECAM Bruxelles pendant mes études d'ingéieur en électronique.

Au-delà du défi technique, l'idée est aussi de tenter de faire quelque chose de
**plus ou moins utile au quotidien**. 

Le cas d'usage visé est celui d'un
**drone d'analyse vidéo de sport d'équipe**, inspiré de VEOCAM:

- Le drone **décolle au début du match** et filme la rencontre.
- Il **suit mes actions tout seul** et se repositionne en permanence **au
  meilleur endroit** pour cadrer le jeu (hauteur, angle, distance), sans
  intervention manuelle.
- À la **mi-temps**, il revient **se poser sur sa station et se recharger**
  automatiquement.
- Il **redécolle et filme la deuxième mi-temps**, puis revient se poser en fin
  de match.
- Un backup existe quand même avec une manette


Les vidéos récupérées permettent ensuite une analyse sportive (placement,
déplacements, phases de jeu), comme le font les solutions professionnelles de
suivi automatique, mais en version personnelle et open source. Ce scénario
concret justifie chaque brique du projet : le suivi autonome motive la vision
et l'algorithme intelligent de la station, la recharge à la mi-temps motive la
station inductive, et la planification de mission par coordonnées permet de
définir les zones de vol autorisées autour du terrain.

## Présentation et objectifs

Autosport tracking est un projet personnel multidisciplinaire visant à concevoir un petit drone
dont les pièces structurelles sont conçues et imprimées en 3D, capable de
rejoindre une station au sol, de la reconnaître par vision, d'effectuer un
atterrissage assisté, puis de recharger sa batterie au moyen d'un système
inductif expérimental.

Particularité de l'architecture : **le « cerveau » du système ne se trouve pas
dans le drone, mais dans la station**. Le drone est un exécutant : il reçoit des
commandes et renvoie son flux vidéo ainsi que son état interne (batterie,
capteurs, états, alarmes). Deux modes de contrôle sont possibles :

- **Mode manette** : connexion directe entre une manette et le drone pour un
  pilotage manuel.
- **Mode station intelligente** : la station pilote le drone. Depuis un PC,
  l'utilisateur encode sur une carte (type maps) une succession de coordonnées,
  que l'**algorithme intelligent interne de la station** traduit en séquence de
  commandes. La station n'envoie que des commandes et reçoit les vidéos ainsi
  que l'état interne du robot.

Le projet sert de fil conducteur pour mettre en pratique plusieurs domaines :
conception mécanique et impression 3D, systèmes temps réel, microcontrôleurs,
traitement du signal, radiofréquences, vision artificielle, électronique
de puissance, gestion de batterie, sûreté et validation expérimentale.

### Objectifs fonctionnels

1. Concevoir en CAO et imprimer en 3D les pièces du drone (châssis, bras,
   supports, protections), puis assembler et mettre au point un drone stable à
   partir d'un contrôleur de vol éprouvé.
2. Développer une électronique embarquée autour d'un STM32 exécutant FreeRTOS.
3. Développer la **station intelligente** : interface cartographique sur PC,
   planification de mission par coordonnées, algorithme interne de traduction
   en commandes, supervision du flux vidéo et de l'état du drone.
4. Établir une liaison radio fiable transportant commandes, télémétrie et
   vidéo, et mesurer ses performances.
5. Traiter la vidéo reçue au sol pour détecter la station (marqueur
   AprilTag/ArUco) et estimer la position relative du drone.
6. Concevoir la station au sol : détection de présence, contrôle d'alignement
   et recharge inductive à puissance limitée.

## Architecture générale

L'architecture est divisée en trois sous-systèmes.

### Drone (pièces imprimées en 3D)

- **Structure imprimée en 3D** : châssis, bras, supports et protections conçus
  en CAO puis imprimés. L'impression 3D permet des itérations rapides et le
  remplacement facile de pièces cassées.
- **Plateforme de vol** : moteurs brushless, ESC, hélices, batterie et
  contrôleur de vol commercial compatible avec un autopilote reconnu. La
  stabilisation et les fonctions critiques de vol restent confiées à ce
  contrôleur.
- **STM32 + FreeRTOS** : supervision des capteurs, gestion d'énergie,
  télémétrie (batterie, états, alarmes), surveillance et **exécution des
  commandes reçues** de la manette ou de la station.
- **Caméra embarquée** : le flux vidéo est transmis à la station ; aucun
  traitement d'image lourd n'est embarqué.
- **Radio** : liaison commande/télémétrie distincte du flux vidéo lorsque
  possible, avec mesure du RSSI, du taux de pertes et de la latence.

Le drone est volontairement « simple » : il exécute des commandes et renvoie la
vidéo et son état interne. L'intelligence de mission est au sol.

### Station intelligente (le cerveau)

- **Héberge l'intelligence du système** : planification de mission et
  algorithme intelligent interne.
- **Interface cartographique sur PC** : encodage d'une succession de
  coordonnées sur une carte ; l'algorithme de la station les traduit en
  séquence de commandes (trajectoire, actions, contraintes de sécurité).
- **Communication descendante minimale** : la station n'envoie que des
  commandes au drone.
- **Communication remontante** : réception du flux vidéo et de l'état interne
  du robot (niveau de batterie, tensions, températures, états, alarmes),
  journalisation et interface de supervision.
- **Vision au sol** : traitement du flux vidéo reçu (OpenCV) pour détecter le
  repère visuel de la station, estimer la position relative du drone et générer
  les commandes d'atterrissage assisté.
- **Surface d'atterrissage** avec repère visuel connu (AprilTag/ArUco).
- **Recharge inductive** : détection de présence, estimation de l'alignement
  des bobines, bobine émettrice, étage de conversion haute fréquence, commande
  de puissance, mesures (courant, tension, température) et arrêt matériel en
  cas de surintensité, surchauffe ou condition anormale.

### Modes de contrôle

| Mode | Liaison | Description |
|------|---------|-------------|
| **Manette** | Manette ↔ drone | Pilotage manuel direct, sans passer par la station. |
| **Station intelligente** | PC → station → drone | Mission encodée sur carte, traduite par l'algorithme interne ; la station envoie les commandes et reçoit la vidéo + l'état du drone. |

### Chaîne d'énergie inductive

```text
Alimentation DC → Onduleur HF → Bobine TX → Bobine RX
              → Redressement → Convertisseur DC/DC → Gestion batterie
```

Les premières expériences seront réalisées à faible puissance sur établi, avec
une alimentation limitée en courant et une charge de test. La recharge d'une
batterie de vol ne sera envisagée qu'après caractérisation électrique et
thermique, et mise en place des protections adaptées.

## Compétences et aspects techniques à travailler

### Conception mécanique et impression 3D

- CAO paramétrique (FreeCAD, Fusion 360, OpenSCAD ou équivalent).
- Conception pour l'impression 3D : orientation des pièces, supports,
  tolérances, assemblages, fixation des moteurs et de l'électronique.
- Choix des matériaux (PLA, PETG, ABS) : rigidité, résistance aux chocs et aux
  vibrations, comportement en température.
- Itérations rapides et impression de pièces de rechange.

### Systèmes embarqués et temps réel

- Architecture firmware en C/C++ pour STM32.
- FreeRTOS : tâches, priorités, files de messages, sémaphores, mutex,
  temporisations et watchdog.
- Drivers et bus de communication : UART, SPI, I²C, GPIO, PWM et éventuellement
  CAN.
- Acquisition de capteurs, filtrage, horodatage et diagnostic.
- Gestion des états : initialisation, veille, fonctionnement normal, défaut et
  arrêt sécurisé.

### Vision artificielle et reconnaissance de la station

- Réception et synchronisation du flux vidéo au sol.
- Calibration de caméra.
- Prétraitement d'image : conversion, seuillage, filtrage et détection de
  contours.
- Détection d'un marqueur AprilTag/ArUco pour identifier la station.
- Estimation de la position et de l'orientation relatives.
- Filtrage des mesures et génération des consignes envoyées au drone.
- Prise en compte de la latence bout en bout (capture → transmission →
  traitement → commande).
- Évolution possible vers une détection d'objet par réseau neuronal, après
  validation d'une solution simple et déterministe.

### Radiofréquences et communications

- Choix des bandes et modules radio conformes à la réglementation applicable.
- Notions d'antennes, polarisation, diagramme de rayonnement et adaptation.
- Mesures de RSSI, pertes de paquets, latence et portée en environnement
  contrôlé.
- Séparation logique entre commande critique et vidéo.
- Définition d'un protocole de commandes robuste (la station n'envoie que des
  commandes ; le drone renvoie vidéo et état interne).
- Étude de la diversité d'antennes comme amélioration éventuelle.

### Électronique de puissance et recharge inductive

- Convertisseurs DC/DC et commutation de MOSFET.
- Onduleur haute fréquence et circuit résonant bobine-condensateur.
- Couplage magnétique, fréquence de résonance, facteur de qualité et rendement.
- Redressement, régulation et mesure de puissance transférée.
- Effets du désalignement des bobines et échauffement.
- Protections : surintensité, surtension, surchauffe et détection de défaut.

### Sûreté, sécurité et validation

- **Perte de liaison** : point critique, l'intelligence étant au sol — le drone
  doit disposer d'un comportement de repli autonome (maintien, retour ou
  atterrissage sûr), défini et testé.
- Batterie faible : procédure de retour ou d'atterrissage sûr.
- Surveillance de température, tension et courant.
- Watchdog matériel/logiciel et arrêt d'urgence.
- Authentification des commandes lorsque la plateforme le permet.
- Tests unitaires, tests d'intégration, journalisation et analyse des
  défaillances.


## Résultat attendu

À l'issue du projet, j'aimerais avoir le drone foncionnel avec toute une documentation dans ce repo.