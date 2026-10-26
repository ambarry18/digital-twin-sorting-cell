# Cahier des charges

## 1. Objet
Simuler une cellule robotisée de tri de cartons (bleu / rouge) pilotée par un automate
programmable (OpenPLC) et supervisée par des indicateurs de production.

## 2. Principe d'architecture
- L'automate est maître de la séquence : modes, arrêt d'urgence, autorisation de cycle.
- ROS 2 / Gazebo simulent le procédé (convoyeur, cartons, caméra, bras) et les capteurs.
- Le pont Modbus TCP est l'unique interface entre les deux.
  Aucun nœud ROS ne commande le convoyeur ni le bras sans passer par l'automate.

## 3. Fonctions
| Réf. | Fonction | Exigence |
|---|---|---|
| F1 | Convoyage | Le convoyeur n'avance que si l'automate l'autorise et que le robot est prêt. |
| F2 | Arrivée | Un capteur TOR (i_part_arrived) arrête le carton en zone de prise, indépendamment de la vision. |
| F3 | Identification | La vision classe chaque carton : catégorie A (bleu), B (rouge) ou inconnue. |
| F4 | Tri | Le bras dépose le carton dans le bac A ou le bac B selon sa catégorie. |
| F5 | Modes | L'automate gère ARRÊT, MARCHE, ARRÊT EN FIN DE CYCLE, DÉFAUT et ARRÊT D'URGENCE. |
| F6 | Arrêt d'urgence | Prioritaire sur tout, mémorisé. Reprise : acquittement (reset), puis commande de marche distincte. |
| F7 | Défauts | Détection : carton inconnu, robot en échec, vision muette (watchdog), arrivée bloquée, temporisations dépassées. Reprise après acquittement. |
| F8 | Indicateurs | Calculés hors automate et journalisés en CSV, avec des courbes. |

## 4. Indicateurs
Cadence ; répartition A/B ; temps de cycle (moyenne, écart-type) ; temps par état ;
nombre de défauts par code ; taux d'erreur de tri.
La vérité terrain (couleur réelle du carton) est fournie par Gazebo et journalisée à part.
Toutes les valeurs sont mesurées en simulation. Aucune valeur n'est fixée a priori.

## 5. Choix v1 / v2
- Vision par couleur (HSV) en v1 ; lecture d'une référence (QR code) en extension.
- Carton inconnu = DÉFAUT en v1 ; v2 : bac « à vérifier » avec escalade en défaut au-delà d'un seuil.

## 6. Hors périmètre
- Pas de fonction de sécurité certifiée : l'arrêt d'urgence est simulé, aucune conformité
  ISO 13849 n'est revendiquée.
- Pas de matériel réel : les performances sont celles de la simulation.

## 7. Critères de réussite
- C1 : N cartons mixtes sont triés sans intervention, avec un taux d'erreur de tri mesuré (N : TBD).
- C2 : un arrêt d'urgence déclenché en plein cycle fige la cellule ; la reprise exige reset puis start.
- C3 : les scénarios de défaut (arrêt d'urgence en cycle, carton inconnu, vision muette, reprise après défaut) sont reproduits et filmés.
- C4 : le CSV et les courbes sont générés automatiquement à partir d'une exécution.

## 8. Valeurs à mesurer (TBD)
Temporisation de stabilisation, délais de défaut (identification, robot, watchdog vision), durée du TON d'arrivée bloquée, nombre N de cartons de test.
