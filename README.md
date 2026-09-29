# 2627_2A_FPGA_POUBLAN_BOUCHE
# TP1 : Tutoriel Quartus
## Allumage de la led
Dans devions programmer un composant permettant d'allumer la LED 0 lorsqu'on appuye sur le bouton poussoir de l'encodeur gauche.
Un code était déjà proposé par l'énoncé mais il fallait le corriger car il éteignait la led au lieu de l'allumé.

Pour cela, on ajoute la fonction *not* à la ligne ```led0 <= pushl;``` pour inverser l'allumage.

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

## Clignotement de la led
Le sujet proposait un morceau de code à intégrer à notre projet. Nous y avons apporté quelques modifications :

Tout d'abord, nous avons changé la fréquence de clignotement car 50MHz ne permet pas de voir le clignotement, on opte pour 500Hz.

Ensuite, on corrige pour la boucle ```if``` pour s'assurer que la led change bien d'état en mettant l'instruction ```not```  dans la ligne ```r_led_enable <= not(r_led_enable);```.

Enfin, on n'oublie pas d'assigné chaque signal à un pin en utilisant ```pin planner```

### Code complet
```vhdl
library ieee;
use ieee.std_logic_1164.all;

entity tuto_fpga is
    port (
        i_clk : in std_logic;
        i_rst_n : in std_logic;
        o_led : out std_logic
    );
end entity tuto_fpga;

architecture rtl of tuto_fpga is
    signal r_led_enable : std_logic := '0';
begin
process(i_clk, i_rst_n)
    variable counter : natural range 0 to 2500000 := 0;
begin
    if (i_rst_n = '0') then
        counter := 0;
        r_led_enable <= '0';
    elsif (rising_edge(i_clk)) then
        if (counter = 2500000) then
            counter := 0;
            r_led_enable <= not(r_led_enable);
        else
            counter := counter + 1;
        end if;
    end if;
end process;
    o_led <= r_led_enable;
end architecture rtl;
```
### Schéma du composant
<img width="1612" height="425" alt="image" src="https://github.com/user-attachments/assets/9b0d8981-67b5-4bff-8fbe-ed5d6bb48aa6" />

On a bien les boucles ```if``` et le compteur, et on a le comportement demandé.


## Chenillard
### Méthode 1 (Fonctionnelle)
On reprend le code précédent que l'on va modifier pour faire un chenillard. On remplace le signal ```r_led_enable``` par un signal compteur ```counter_led``` déplacer la led qui s'allume sur la carte. On implémente un bus ```o_led``` dont chaque valeur est assigné à une pin pour allumer une led. 

On fait 2 *process* car on veut que ces 2 parties de code fonctionnent **simultanément**, on veut que le compteur d'horloge s'exécute en même temps que l'allumage des leds. Dans le second *process*, on utilise les boucles if et le compteur ```counter_led``` pour qu'à chaque *front montant d'horloge* une led s'allume et sa précédente s'éteint.

**Attention** : chaque signal doit être modifié dans un seul process sinon on a des erreurs causés par le multithreading. Donc on fait bien attention à modifier ```counter_led``` seulement dans le premier process, ```r_led```seulement dans le deuxième et ```o_led``` seulement en dehors des process. On utilise d'ailleurs un buffer ```r_led``` pour éviter de modifier directement le signal de sortie ```o_led``` dans les process. 

```vhdl

library ieee;
use ieee.std_logic_1164.all;

entity tuto_fpga is
    port (
        i_clk : in std_logic;
        i_rst_n : in std_logic;
        o_led : out std_logic_vector(9 downto 0)
    );
end entity tuto_fpga;

architecture rtl of tuto_fpga is
	 signal counter_led : natural range 0 to 9 :=0;
	 signal r_led : std_logic_vector(9 downto 0);
begin
process(i_clk, i_rst_n)
    variable counter_clk : natural range 0 to 2500000 := 0;
begin
    if (i_rst_n = '0') then
        counter_clk := 0;
		  counter_led <= 0;
    elsif (rising_edge(i_clk)) then
        if (counter_clk = 2500000) then
            counter_clk := 0;
			counter_led <= counter_led +1;
				if (counter_led = 10) then
					counter_led <= 0 ;
				end if;
        else
            counter_clk := counter_clk + 1;
        end if;
    end if;
end process;
process(i_clk, i_rst_n)
begin
	if (i_rst_n = '1') then
		if  (counter_led=0) then
			r_led(0) <= not(r_led(0));
			r_led(9) <= '0';
		else
			r_led(counter_led-1) <= '0';
			r_led(counter_led) <= '1';
		end if;
	else
		r_led <= (others =>'0');
	end if;
end process;
	o_led <= r_led;

end architecture rtl;

```
### Méthode 2 (Mieux)
On va simplifier le code en supprimant le compteur ```counter_led```.

**Principe** : On prend un motif sur 10 bits (ici 0000000001) et on le déplace d'un bit à chaque front montant d'horloge. Ainsi, on change toutes les valeurs ```r_led``` en même temps et le code fonctionne pour n'importe quel motif.



code du prof, a tester avec un tb sur modelsim
``` vhdl
library ieee;
use ieee.std_logic_1164.all;

entity tuto_fpga is
    port (
        i_clk : in std_logic;
        i_rst_n : in std_logic;
        o_led : out std_logic_vector(9 downto 0)
    );
end entity tuto_fpga;

architecture rtl of tuto_fpga is
	 signal enable_led : std_logic;
	 signal r_led : std_logic_vector(9 downto 0) := "0000000001";
begin

process(i_clk, i_rst_n)
    variable counter_clk : natural range 0 to 2500000 := 0;
begin
    if (i_rst_n = '0') then
        counter_clk := 0;
    elsif (rising_edge(i_clk)) then
        if (counter_clk = 2500000) then
            counter_clk := 0;
				enable_led <= '1';
        else
            enable_led <= '0';
				counter_clk := counter_clk + 1;
        end if;
    end if;
end process;


process(i_clk, i_rst_n)
begin
	if (i_rst_n = '1') then
		if(enable_led = '1') then
			r_led <= r_led(8 downto 0) & r_led(9);
		end if;
	else
		r_led <= "0000000001";
	end if;
end process;

o_led <= r_led;

end architecture rtl;

```

