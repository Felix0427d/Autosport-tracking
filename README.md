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
2. Développer une électronique embarquée autour d'un ESP32 exécutant FreeRTOS
   (microcontrôleur simple et économique, FreeRTOS natif), dédié à la chaîne
   de recharge inductive : détection de présence, mesures, protections et
   communication avec la station pendant la charge. Son Wi-Fi/Bluetooth
   intégré suffit à courte portée (recharge, configuration au sol), mais la
   liaison de vol au-delà d'une centaine de mètres reposera sur un module
   radio longue portée dédié (cf. objectif 4).
3. Développer la **station intelligente** : interface cartographique sur PC,
   planification de mission par coordonnées, algorithme interne de traduction
   en commandes, supervision du flux vidéo et de l'état du drone.
4. Établir une liaison radio fiable transportant commandes, télémétrie et
   vidéo, et mesurer ses performances.
5. Traiter la vidéo reçue au sol pour détecter la station (marqueur
   AprilTag/ArUco) et estimer la position relative du drone.
6. Concevoir la station au sol : détection de présence, contrôle d'alignement
   et recharge inductive à puissance limitée.


## Résultat attendu

À l'issue du projet, j'aimerais avoir le drone foncionnel avec toute une documentation dans ce repo.