# Spécifications matérielles du drone

## Configuration retenue : « endurance » 

| Paramètre | Valeur cible |
|-----------|--------------|
| Masse au décollage (MTOW) | ~3 kg max |
| Hélices | 13" (commerciales, nylon renforcé) |
| Empattement moteur-à-moteur (diagonale) | ~550–600 mm |
| Encombrement au sol (avec hélices) | ~80 × 80 cm |
| Hauteur | ~20–25 cm |
| Autonomie visée | ~45 min |
| Moteurs | 4 × bas KV (~280–380 KV), poussée ~1,2 kg chacun |
| Batterie | Li-ion 6S haute densité (21700, type Molicel P45B), ~1 100 g, ~230–275 Wh |
| Contrôleur de vol | Autopilote éprouvé (ArduPilot/Pixhawk ou équivalent) + GPS |
| Microcontrôleur auxiliaire | ESP32 (FreeRTOS) |
| Caméras | 4 × (références à définir) |
| Radio longue portée | Module dédié > 100 m (ELRS ou équivalent) |

## Budget de poids

| Poste | Poids |
|-------|------:|
| Châssis (hybride carbone + 3D) | 300–400 g |
| 4 moteurs + 4 ESC | 300–350 g |
| Hélices 13" | 40 g |
| Contrôleur de vol + GPS + récepteur | 60 g |
| ESP32 + PCB + câblage | 50 g |
| 4 caméras + émetteur(s) vidéo | 80–120 g |
| Batterie 6S Li-ion haute capacité | ~1 100 g |
| Divers (visserie, amortisseurs, marge) | 100 g |
| **Total** | **~2 100–2 200 g** |

Marge disponible jusqu'à 3 kg : ~800 g (réserve pour évolutions, protections,
bobine de réception inductive).

## Châssis : approche hybride carbone + impression 3D

- **Bras et plaques principales** : carbone (rigidité native, bras de ~25 cm
  soumis à flexion et vibrations basses fréquences).
- **Imprimé en 3D** : corps central, supports caméras, carénages, protections
  d'hélices, amortisseurs TPU, logement batterie, interface station de
  recharge, pièces de rechange.
- **Hélices** : commerciales uniquement — jamais imprimées (sécurité,
  équilibrage, rendement).

## Contraintes de conception CAO

- Châssis paramétrique (diamètre d'hélice en variable) pour permettre des
  variantes sans tout redessiner.
- Points d'ancrage moteurs renforcés.
- Amortissement des vibrations pour le contrôleur de vol et les caméras
  (éléments TPU).
- Station d'atterrissage associée : plateau ≥ 1 m × 1 m, marqueur
  AprilTag/ArUco détectable depuis 10–15 m de haut.

## Points de vigilance

- **Réglementation** : à 2–3 kg, catégorie ouverte A3 (voire spécifique) en
  Belgique/UE — distances aux personnes non impliquées, enregistrement et
  formation du télépilote. Filmer au-dessus d'un match = point sensible.
- **Sécurité** : 2–3 kg en chute = dégâts importants. Protections d'hélices
  imprimées quasi indispensables au-dessus d'un terrain.
- **Autonomie match** : même à 45 min visés, prévoir 2–3 batteries en
  rotation ; la recharge inductive reste un démonstrateur, pas la solution
  d'autonomie.
