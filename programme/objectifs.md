# Programme hebdomadaire

| Semaine | Objectifs |
|---------|-----------|
| 1 | Trouver financement |
| 2 | Définir les dimensions du drone (moteurs, contrôleur de vol, batterie, caméra) et installer FreeCAD |
| 3 | Modéliser le châssis v1 en CAO : corps central, bras, supports moteurs |
| 4 | CAO : intégrer l'électronique (emplacements contrôleur de vol, ESP32, caméra, batterie) et les protections |
| 5 | Imprimer le châssis v1, vérifier l'assemblage à blanc, itérer sur la CAO |
| 6 | Établir le schéma électrique complet du drone (alimentation, ESP32, caméra, radio, contrôleur de vol) |
| 7 | Schéma électrique : vérification, choix définitif des composants, nomenclature |
| 8 | Câblage prototype sur platine d'essai : ESP32 + caméra + alimentation |
| 9 | Firmware ESP32 : structure FreeRTOS (tâches, files, watchdog) et acquisition caméra |
| 10 | Firmware ESP32 : télémétrie (batterie, états, alarmes) et gestion des commandes reçues |
| 11 | Simulation en labo : banc de test complet, vérification du comportement attendu (caméra + télémétrie + commandes) |
| 12 | Simulation en labo : cas limites (perte de liaison, batterie faible, redémarrage), correction du firmware |
| 13 | Conception du PCB sous KiCad à partir du schéma validé en labo |
| 14 | PCB : routage, relecture, envoi en fabrication |
| 15 | Recharge inductive : étude théorique (bobines, résonance, rendement) et dimensionnement |
| 16 | Recharge inductive : prototype basse puissance sur établi, premières mesures |
| 17 | Recharge inductive : mesures de rendement, alignement, échauffement, protections |
| 18 | Réception et soudure du PCB, tests électriques à vide puis en charge |
| 19 | Intégration de la recharge inductive au PCB/station, séquence de charge contrôlée |
| 20 | Assemblage complet : châssis CAO + PCB + moteurs + caméra, vérifications au sol |
| 21 | Tests au sol sans hélices, puis premiers vols manuels à la manette |
| 22 | Réglages de stabilité, validation du comportement en vol du PCB et de la caméra |
| 23 | Télécommunication : choix et réception du module radio longue portée (ELRS ou équivalent) |
| 24 | Télécommunication : liaison commande + télémétrie drone ↔ station, mesures de portée et latence |
| 25 | *(en parallèle)* Vision : réception et affichage du flux vidéo au sol, calibration caméra |
| 26 | *(en parallèle)* Vision : détection du marqueur AprilTag/ArUco, estimation de pose |
| 27 | *(en parallèle)* Intelligence : interface cartographique PC, encodage des coordonnées de mission |
| 28 | *(en parallèle)* Intelligence : algorithme de traduction coordonnées → commandes, suivi de cible |
| 29 | Intégration télécommunication + vision + intelligence dans la station |
| 30 | Première mission autonome simple (waypoints), comportements de repli testés |
| 31 | Essais d'atterrissage assisté progressifs guidés par la vision |
| 32 | Séquence complète : mission → retour → atterrissage → recharge |
| 33 | Scénario match : décollage → suivi → mi-temps/recharge → 2ᵉ période |
| 34 | Fiabilisation, journalisation, analyse des défaillances |
| 35 | Documentation finale : schémas, code, plans CAO/STL, procédures de test |
| 36 | Analyse des limites du prototype et pistes d'amélioration |
