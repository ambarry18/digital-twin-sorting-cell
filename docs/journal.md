# Journal de bord

## 2026-10-26 — Étape 0 : cadrage

### Décisions
- Architecture : l'automate (OpenPLC) est maître de la séquence ; ROS 2/Gazebo simule le procédé ;
  le pont Modbus est l'unique interface. Raison : l'arrêt d'urgence doit passer par l'automate.
- Tri par catégorie (A = bleu, B = rouge), sans notion bon/mauvais. Raison : cahier des charges
  plus simple et plus clair.
- Vision : couleur en v1, QR code en extension. L'automate ne reçoit qu'un identifiant de
  catégorie (i_part_cat) ; une table categories.yaml associe catégorie et bac.
- Carton inconnu : DÉFAUT en v1. En v2 : bac « à vérifier » avec escalade en défaut au-delà d'un seuil.
- Arrêt d'urgence : mémorisé, acquittement (reset), puis marche distincte (start).
- Ajout de i_part_arrived (capteur TOR d'arrivée), distinct de la vision, avec un TON de blocage.
- Programmation : Grafcet dessiné (référence) + implémentation en ST ; test du SFC à l'étape 1.
- Indicateurs calculés hors automate (pont/journal), à partir de o_state, de i_robot_done et d'horodatages.

### À vérifier plus tard
- Mapping des adresses OpenPLC (%IX/%QX/%IW/%QW) vers les adresses Modbus.
- Sens de la liaison Modbus (OpenPLC serveur ou maître).
- Fiabilité du SFC dans OpenPLC.
- Toutes les temporisations (T_settle, watchdogs, PT du TON) : TBD, à mesurer en simulation.
