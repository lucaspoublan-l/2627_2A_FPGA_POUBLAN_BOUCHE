# 2627_2A_FPGA_POUBLAN_BOUCHE
# TP1 : Tutoriel Quartus
### Allumage de la led
Dans devions programmer un composant permettant d'allumer la LED 0 lorsqu'on appuye sur le bouton poussoir de l'encodeur gauche.
Un code était déjà proposé par l'énoncé mais il fallait le corriger car il éteignait la led au lieu de l'allumé.

Pour cela, on ajoute la fonction *not* à la ligne *led0 <= pushl;* pour inverser l'allumage.

**Code corrigé :**
```vhdl
library ieee;
use ieee.std_logic_1164.all;

entity tuto_fpga is
    port (
        pushl : in std_logic;
        led0 : out std_logic
    );
end entity tuto_fpga;

architecture rtl of tuto_fpga is
begin
    led0 <= not(pushl);
end architecture rtl;
```

### Clignotement de la led
Le sujet proposait un morceau de code à intégrer à notre projet. Nous y avons apporté quelques modifications :

Tout d'abord, nous avons changé la fréquence de clignotement car 50MHz ne permet pas de voir le clignotement, on opte pour 500Hz.

Ensuite, on modifie le code pour 

Enfin, on n'oublie pas d'assigné chaque signal à un pin en utilisant t```pin planner```
