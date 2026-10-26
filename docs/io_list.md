# Liste des entrées / sorties 

Convention : « entrée » et « sortie » sont vues depuis l'automate.
Les capteurs vitaux sont en logique négative : une valeur à 0 signifie danger ou défaut,
de sorte qu'un fil coupé ou un nœud arrêté conduise à un état sûr.

## Entrées automate

| Nom | Type | Signification | Source |
|---|---|---|---|
| i_estop_ok | BOOL | 1 = circuit d'arrêt d'urgence sain, 0 = enfoncé (NF) | pont (opérateur simulé) |
| i_btn_start | BOOL | Demande de marche | pont |
| i_btn_stop | BOOL | Demande d'arrêt | pont |
| i_btn_reset | BOOL | Acquittement de défaut ou d'arrêt d'urgence | pont |
| i_part_arrived | BOOL | Carton détecté à l'arrivée de la zone de prise (capteur TOR) | Gazebo via pont |
| i_part_present | BOOL | Carton présent en zone de prise, vu par la vision | vision |
| i_part_cat | INT | 0 = aucune, 1 = catégorie A (bleu), 2 = catégorie B (rouge), 3 = inconnue | vision |
| i_vision_hb | INT | Compteur de vie de la vision (watchdog) | vision |
| i_robot_ready | BOOL | Robot disponible | nœud de mission / MoveIt 2 |
| i_robot_done | BOOL | Dépose terminée | nœud de mission / MoveIt 2 |
| i_robot_fault | BOOL | Échec de planification ou d'exécution | nœud de mission / MoveIt 2 |

## Sorties automate

| Nom | Type | Signification | Destination |
|---|---|---|---|
| o_conveyor_run | BOOL | Autorisation de déplacement du convoyeur | Gazebo via pont |
| o_robot_cmd | INT | 0 = rien, 1 = déposer dans le bac A, 2 = déposer dans le bac B | nœud de mission |
| o_light_green | BOOL | Voyant vert (MARCHE) | supervision |
| o_light_orange | BOOL | Voyant orange (ARRÊT) | supervision |
| o_light_red | BOOL | Voyant rouge (DÉFAUT / ARRÊT D'URGENCE) | supervision |
| o_fault_code | INT | Code du défaut actif (table à définir) | supervision / journal |
| o_state | INT | 0 = ARRÊT, 1 = MARCHE, 2 = ARRÊT EN FIN DE CYCLE, 3 = DÉFAUT, 4 = ARRÊT D'URGENCE | journal |

## Règles associées
- o_conveyor_run = 1 uniquement si l'état est MARCHE, si le Grafcet de cycle est en E10 et si i_robot_ready = 1.
- Le watchdog i_vision_hb est incrémenté par la vision ; l'automate lève un défaut si la valeur
  ne change plus pendant une durée TBD.
- Le TON d'arrivée bloquée compte uniquement si o_conveyor_run = 1 et i_part_arrived = 1.

## À définir à l'étape 1 (après lecture de la documentation OpenPLC)
- Adresses automate (%IX / %QX / %IW / %QW) et adresses Modbus correspondantes.
- Table des codes de défaut (o_fault_code).
- Quelles zones sont écrivables par le pont.

