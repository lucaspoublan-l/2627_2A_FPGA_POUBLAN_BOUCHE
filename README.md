# PROJET ECRAN MAGIQUE

## Gestion des encodeurs

### Le schéma de l'encoder est le suivant : 
<img width="1202" height="572" alt="image" src="https://github.com/user-attachments/assets/1fe82048-ec27-40fa-b353-7bc9c3484577" />


### Explications : 
Pour détecter les fronts montants, le FGPA va comparer les registres r_previous et r_current et mettre à 1 le signal s_rising et à 0 s_falling si *r_prev < r_current* et inversement si *r_prev > r_current*. Ensuite le FPGA compare paraléllement les signaux s_a_rising à s_b_rising et s_a_falling à  s_b_falling pour déterminer le sens. Concrètement, si *s_a_rising=s_b_falling=1 et s_a_falling=s_b_rising=0* alors ou tourne dans un sens et inversement pour l'autre sens de rotation.

