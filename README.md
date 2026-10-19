# PROJET ECRAN MAGIQUE

## Gestion des encodeurs

### Le schéma de l'encoder est le suivant : 
<img width="1202" height="572" alt="image" src="https://github.com/user-attachments/assets/4993c2e6-a428-4c4b-aa6d-014acfbdc274" />


### Explications : 
En entrée, on a les données i_A et i_B mesurées par les **deux capteurs A et B** de l'encodeur en temps réel.

On compartimentent le code en 3 process les différentes parties du code ( les bascules, la détection du sens de rotation et le conteur final).

Premièrement, on a **deux bascules D consécutives** pour récupérer la valeur de i_A (*r_cur*) et sa précédente (*r_prev*) que l'on va comparer pour **détecter les fronts montants** de A et B. Ainsi, on va mettre à 1 le signal s_rising et à 0 s_falling si *r_prev < r_current* et inversement si *r_prev > r_current*. 
La sortie des bascules sont remises à 0 lorsqu'on appuie sur le bouton reset.

Ensuite, le FPGA regarde quel capteur est en front montant ainsi que la valeur de la donnée actuelle pour savoir dans quel sens l'encodeur tourne. Concrètement, si *s_a_rising=1* et *r_a_current=1* alors ou tourne dans un sens donc on met clockwise à 1 et cntclock wise à 0 et inversement ; si *s_b_rising=1* et *r_b_current=1* alors on tourne dans le sens inverse. Ainsi, les **signaux  clockwise et cntclockwise** indiquent le **sens de rotation** de l'encodeur.

Enfin, selon le sens de rotation de on ajoute ou soustrait 1 à la **valeur binaire affichée** par les LEDs et on revient à 0 lorsque toutes les leds sont allumées. En pratique, on ajoute 1 quand clockwise vaut 1 et un soustrait 1 quand cntclockwise vaut 1.

