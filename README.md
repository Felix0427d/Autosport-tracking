# AURORA

**Drone autonome avec station intelligente et recharge inductive**

*Autonomous Unmanned Robot with Optimized Recharging Architecture*

> **Budget plafond : 2 500 €** · **Durée indicative : 6 à 12 mois**
>
> Prototype progressif : vol stabilisé, perception visuelle, intelligence au sol,
> électronique numérique et recharge inductive expérimentale.

## Idées générales

Au-delà du défi technique, l'idée est aussi de tenter de faire quelque chose de
**plus ou moins utile au quotidien**. Le cas d'usage visé est celui d'un
**drone d'analyse vidéo de sport d'équipe** :

- Le drone **décolle au début du match** et filme la rencontre.
- Il **suit mes actions tout seul** et se repositionne en permanence **au
  meilleur endroit** pour cadrer le jeu (hauteur, angle, distance), sans
  intervention manuelle.
- À la **mi-temps**, il revient **se poser sur sa station et se recharger**
  automatiquement.
- Il **redécolle et filme la deuxième mi-temps**, puis revient se poser en fin
  de match.

Les vidéos récupérées permettent ensuite une analyse sportive (placement,
déplacements, phases de jeu), comme le font les solutions professionnelles de
suivi automatique, mais en version personnelle et open source. Ce scénario
concret justifie chaque brique du projet : le suivi autonome motive la vision
et l'algorithme intelligent de la station, la recharge à la mi-temps motive la
station inductive, et la planification de mission par coordonnées permet de
définir les zones de vol autorisées autour du terrain.

## Présentation et objectifs

AURORA est un projet personnel multidisciplinaire visant à concevoir un petit drone
dont les pièces structurelles sont **conçues et imprimées en 3D**, capable de
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
FPGA, traitement du signal, radiofréquences, vision artificielle, électronique
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
7. Utiliser un FPGA pour une fonction matérielle ciblée et mesurable.

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
- **FPGA** : fonction matérielle dédiée (acquisition parallèle, horodatage
  matériel, génération de signaux déterministes ou surveillance rapide de
  défauts). Non nécessaire à la stabilisation du premier prototype.
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

### FPGA et logique numérique

- Description matérielle en VHDL ou Verilog.
- Machines à états finis, compteurs, synchronisation et interfaces.
- Simulation, contraintes temporelles et validation sur carte.
- Interface FPGA–STM32 par SPI ou bus parallèle.
- Démonstrateur conseillé : acquisition multi-voies ou détection matérielle
  d'un dépassement de seuil.

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

## Plan de développement proposé

| Phase | Travaux et critères de validation | Durée indicative |
|-------|-----------------------------------|------------------|
| **1. Châssis et socle de vol** | Conception CAO des pièces, impression 3D, assemblage du drone, configuration du contrôleur de vol, tests au sol puis vols manuels encadrés à la manette. | 4–6 semaines |
| **2. Embarqué** | Carte STM32, FreeRTOS, lecture de capteurs, télémétrie (batterie, états) et exécution des commandes reçues. | 4–6 semaines |
| **3. Liaison radio** | Transport commandes / télémétrie / vidéo, mesures de portée, latence et pertes de paquets, sécurisation des fonctions de commande. | 2–4 semaines |
| **4. Station intelligente** | Interface cartographique sur PC, encodage des coordonnées, algorithme interne de traduction en commandes, supervision vidéo + état du drone. | 4–8 semaines |
| **5. Vision** | Réception vidéo au sol, détection de marqueur, estimation de pose et essais sur station fixe avant tout atterrissage autonome. | 4–8 semaines |
| **6. FPGA** | Démonstrateur indépendant, simulation, intégration avec STM32. | 3–6 semaines |
| **7. Recharge** | Prototype inductif basse puissance sur établi, mesures de rendement, alignement et protections. | 6–8 semaines |
| **8. Intégration** | Essais progressifs de reconnaissance, atterrissage assisté commandé par la station, présence sur station et séquence de recharge contrôlée. | 4–8 semaines |

Les durées peuvent se chevaucher. Le calendrier dépendra notamment de la
disponibilité du matériel, du temps de mise au point et de l'accès à un espace
de test adapté.

## Estimation budgétaire

Les montants ci-dessous sont des enveloppes de planification, et non des devis.
Ils comprennent une marge pour les accessoires et les petites pièces. Les prix
réels dépendront des fournisseurs, des frais de livraison et des choix de
cartes.

### Laboratoire

| Poste | Budget estimé |
|-------|--------------:|
| Oscilloscope de laboratoire, idéalement 4 voies | 400 € |
| Multimètre numérique True RMS | 50 € |
| Générateur de fonctions | 80 € |
| Alimentation de laboratoire réglable et limitée en courant | 100 € |
| Analyseur de spectre RF compact d'entrée de gamme | 180 € |
| Soudage, consommables, sondes et câblage de base | 100 € |
| **Imprimante 3D (entrée de gamme) et filament** | **200 €** |
| **Sous-total laboratoire** | **1 110 €** |

### Projet

| Poste | Budget estimé |
|-------|--------------:|
| Moteurs brushless, ESC, hélices et contrôleur de vol (châssis imprimé en 3D) | 380 € |
| Batteries, chargeur adapté et pièces de rechange | 120 € |
| STM32, carte de développement FPGA et interfaces | 130 € |
| Caméra embarquée et transmission vidéo | 90 € |
| Capteurs complémentaires et marqueurs de station | 70 € |
| Radio, antennes et télémétrie | 80 € |
| Prototype inductif : bobines, condensateurs, MOSFET, driver, redressement et régulation | 220 € |
| Station au sol : structure, mesures et protections | 120 € |
| Connectique et consommables supplémentaires | 50 € |
| **Sous-total projet** | **1 260 €** |

### Total

| Poste | Budget estimé |
|-------|--------------:|
| **Total estimé** | **2 370 €** |
| **Réserve pour imprévus** | **130 €** |
| **Budget global prévu** | **2 500 €** |

### Arbitrages budgétaires

Le budget est serré pour réunir simultanément un laboratoire complet, un drone,
une station intelligente et un prototype de recharge. Les arbitrages suivants
sont donc recommandés :

- Acheter un oscilloscope de laboratoire fiable avant d'investir dans des
  instruments spécialisés plus coûteux.
- Emprunter à l'université, si possible, l'analyseur RF, les sondes
  différentielles, les instruments de mesure de puissance **et l'accès à une
  imprimante 3D** (fablab) — l'achat d'une imprimante d'entrée de gamme reste
  prévu au budget par défaut.
- L'impression 3D du châssis supprime l'achat d'un châssis commercial et réduit
  le coût des pièces de rechange (réimpression à l'identique).
- Le traitement de vision étant réalisé au sol, aucun calculateur embarqué
  coûteux n'est nécessaire : une caméra et une transmission vidéo suffisent.
- Utiliser un contrôleur de vol éprouvé plutôt que développer la boucle de
  stabilisation depuis zéro.
- Commencer par un marqueur visuel, puis envisager une reconnaissance plus
  complexe seulement si le temps et le budget le permettent.
- Limiter la recharge inductive à une démonstration basse puissance avant
  d'envisager la recharge effective de la batterie du drone.

## Éléments à ne pas sous-estimer

- **Perte de liaison** : le cerveau étant dans la station, une coupure radio ne
  doit jamais laisser le drone sans réponse. Un comportement de repli autonome
  (maintien, retour ou atterrissage sûr) doit être défini et testé.
- **Résistance mécanique des pièces imprimées** : vibrations, chocs et fatigue
  peuvent fissurer les pièces 3D. Prévoir une inspection régulière et imprimer
  des pièces de rechange à l'avance.
- **Sécurité électrique** : les mesures sur des convertisseurs commutés peuvent
  être dangereuses. Une pince de masse d'oscilloscope ne doit jamais être
  raccordée à un nœud flottant ou commuté sans vérifier la topologie et les
  références de masse. Utiliser une sonde différentielle adaptée lorsque
  nécessaire.
- **Batteries** : utiliser un chargeur et un système de gestion compatibles
  avec la chimie et le nombre de cellules. Ne pas improviser la recharge d'une
  batterie de vol par le prototype inductif.
- **Vol** : réaliser les premiers essais sans hélices lorsque cela est
  pertinent, puis dans une zone de test sécurisée et conformément aux règles
  applicables en Belgique et dans l'Union européenne.
- **Radio** : vérifier les bandes, puissances et conditions d'utilisation
  autorisées avant les essais.
- **Autonomie** : la reconnaissance de la station et l'algorithme de la station
  ne doivent pas commander directement les moteurs sans validation, limites de
  vitesse, procédure d'abandon et mécanismes de sécurité.

## Résultat attendu

À l'issue du projet, le résultat visé est un démonstrateur documenté dont le
châssis et les pièces structurelles sont imprimés en 3D, capable de voler sous
le contrôle d'un autopilote éprouvé, pilotable **à la manette ou par la station
intelligente** (mission de coordonnées encodée sur carte et traduite par
l'algorithme interne). La station envoie les commandes, reçoit la vidéo et
l'état interne du drone, détecte visuellement la zone d'atterrissage et réalise
une séquence d'approche/atterrissage assisté. Une station de recharge inductive
expérimentale démontrera le transfert d'énergie à faible puissance, avec
mesures de rendement et protections.

Le livrable final comprendra les **fichiers CAO et STL des pièces imprimées**,
les schémas électroniques, le code STM32, les sources FPGA, le logiciel de la
station intelligente (interface cartographique, algorithme de mission,
traitement vidéo), les plans de la station, les résultats de mesure, les
procédures de test et une analyse des limites du prototype.

---

*Document de cadrage initial — les choix de composants et le budget devront
être confirmés avant commande.*
