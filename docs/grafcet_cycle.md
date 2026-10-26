# Grafcet du cycle de tri

Ce Grafcet est encapsulé dans l'étape X1 (MARCHE) du Grafcet des modes. Il repart de E10 après un défaut, un arrêt d'urgence ou un arrêt en fin de cycle.

## Étapes

| Étape | Rôle | Actions |
|---|---|---|
| E10 | Attente d'un carton | o_conveyor_run = 1, o_robot_cmd = 0 |
| E11 | Stabilisation | convoyeur off, temporisation T_settle (TBD) |
| E12 | Identification | attente de la classification par la vision |
| E13 | Dépose en cours | o_robot_cmd = 1 (bac A) ou 2 (bac B), maintenu jusqu'à la fin |
| E14 | Fin de dépose | o_robot_cmd = 0, attente du retour du robot à l'état prêt |

## Transitions

| De → vers | Réceptivité |
|---|---|
| E10 → E11 | i_part_arrived |
| E11 → E12 | T_settle écoulée |
| E12 → E13 | i_part_present · (i_part_cat = 1 ou i_part_cat = 2) |
| E13 → E14 | i_robot_done |
| E14 → E10 | NOT i_robot_done · i_robot_ready |

Si i_part_cat = 3 (carton inconnu), ce Grafcet ne fait rien : la transition X1 → X3
du Grafcet des modes prend le relais.

## Défauts de temporisation (v1)
- E12 : la vision ne rend aucune classification dans le délai prévu. Défaut « identification ».
- E13 : i_robot_done n'arrive pas dans le délai prévu. Défaut « robot ».
Les durées sont TBD, à mesurer en simulation.

## Points de conception
1. Poignée de main à quatre phases (E13 → E14 → E10). L'automate pose la commande,
   le robot l'exécute puis lève i_robot_done. L'automate retire alors la commande,
   et le robot baisse i_robot_done. Chaque signal est acquitté par l'autre côté.
   C'est plus fiable qu'une simple impulsion, d'autant que la liaison Modbus passe
   par un pont à scrutation périodique.
2. La commande o_robot_cmd reste maintenue en E13 : une impulsion pourrait tomber
   entre deux lectures du pont.
3. o_conveyor_run vaut 1 uniquement en E10 avec X1 actif et i_robot_ready à 1.
   Le convoyeur ne repart pas tant que le robot n'est pas revenu à l'état prêt.
