# PROJET ECRAN MAGIQUE

## Gestion des encodeurs

### Le schéma de l'encoder est le suivant : 
<img width="1202" height="572" alt="image" src="https://github.com/user-attachments/assets/907a3872-dc6e-4772-b31f-c6bda2c1c9cf" />



### Explications : 
En entrée, on a les données i_A et i_B mesurées par les deux capteurs dans l'encodeur en temps réel.
Premièrement, on a deux bascules D consécutives pour récupérer la valeur de i_A (*r_cur*) et sa précédente (*r_prev*) que l'on va comparer pour détecter les fronts montants de A et B. Ainsi, on va mettre à 1 le signal s_rising et à 0 s_falling si *r_prev < r_current* et inversement si *r_prev > r_current*. 
La sortie des bascules sont remises à 0 lorsqu'on appuie sur le bouton reset.

Ensuite le FPGA compare paraléllement les signaux s_a_rising à s_b_rising et s_a_falling à  s_b_falling pour déterminer le sens. Concrètement, si *s_a_rising=s_b_falling=1 et s_a_falling=s_b_rising=0* alors ou tourne dans un sens et inversement pour l'autre sens de rotation.

Enfin, selon le sens de rotation de on ajoute ou soustrait 1 à la valeur binaire finale affichée par les LEDs et on revient à 0 lorsque toutes les leds sont allumées.

