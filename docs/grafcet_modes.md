# Grafcet de gestion des modes

Ce Grafcet décide uniquement de l'état de la cellule. Le cycle de tri est un second Grafcet, encapsulé dans l'étape X1 (voir grafcet_cycle.md). Structure inspirée du GEMMA
(familles A : arrêts, F : fonctionnement, D : défaillance).

## Étapes

| Étape | Nom (famille GEMMA) | Actions |
|---|---|---|
| X0 (initiale) | ARRÊT (A1) | o_state = 0, convoyeur off, o_robot_cmd = 0, voyant orange |
| X1 | MARCHE (F1) | o_state = 1, voyant vert, cycle de tri autorisé (Grafcet encapsulé) |
| X2 | ARRÊT EN FIN DE CYCLE (A2) | o_state = 2, pas de nouveau carton, convoyeur off, voyant orange |
| X3 | DÉFAUT (D) | o_state = 3, voyant rouge, convoyeur off, o_robot_cmd = 0, o_fault_code mémorisé |
| X4 | ARRÊT D'URGENCE (D) | o_state = 4, voyant rouge, tout à l'arrêt |

## Transitions

| De → vers | Réceptivité |
|---|---|
| X0 → X1 | i_btn_start (front montant) · i_estop_ok · i_robot_ready |
| X1 → X2 | i_btn_stop (front montant) |
| X2 → X0 | i_robot_ready |
| X1 → X3, X2 → X3 | un défaut actif |
| X3 → X0 | i_btn_reset (front montant) · aucun défaut actif |
| X0, X1, X2, X3 → X4 | NOT i_estop_ok |
| X4 → X0 | i_estop_ok · i_btn_reset (front montant) |

## Défauts actifs (v1)
- i_part_cat = 3 (carton inconnu)
- i_robot_fault
- watchdog de i_vision_hb (valeur inchangée au-delà d'une durée TBD)
- i_part_arrived bloqué à 1 alors que le convoyeur tourne (TON, durée TBD)
- temporisations dépassées dans le Grafcet de cycle (identification, dépose)

## Règles de conception
1. Priorité de l'arrêt d'urgence : i_estop_ok est testé avant toute autre condition.
2. Pas de redémarrage automatique : X3 et X4 reviennent à X0, jamais à X1.
   Il faut un reset, puis un nouvel appui sur start.
3. Fronts montants (R_TRIG) sur start, stop et reset.
4. À la sortie de X1 (vers X2, X3 ou X4), le Grafcet de cycle est réinitialisé en E10.

