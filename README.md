# PROJET ECRAN MAGIQUE

# Séance 1 : Gestion des encodeurs

## Le schéma de l'encoder est le suivant : 
<img width="1202" height="572" alt="image" src="https://github.com/user-attachments/assets/1a344311-b114-4ea0-a35b-7d9b744cdada" />



## Explications : 
En entrée, on a les données i_A et i_B mesurées par les **deux capteurs A et B** de l'encodeur en temps réel.

On compartimentent le code en 3 process les différentes parties du code ( les bascules, la détection du sens de rotation et le conteur final).

Premièrement, on a **deux bascules D consécutives** pour récupérer la valeur de i_A (*r_cur*) et sa précédente (*r_prev*) que l'on va comparer pour **détecter les fronts montants** de A et B. Ainsi, on va mettre à 1 le signal s_rising et à 0 s_falling si *r_prev < r_current* et inversement si *r_prev > r_current*. 
La sortie des bascules sont remises à 0 lorsqu'on appuie sur le bouton reset.

Ensuite, le FPGA regarde quel capteur est en front montant ainsi que la valeur de la donnée actuelle pour savoir dans quel sens l'encodeur tourne. Concrètement, si *s_a_rising=1* et *r_a_current=1* alors ou tourne dans un sens donc on met clockwise à 1 et cntclock wise à 0 et inversement ; si *s_b_rising=1* et *r_b_current=1* alors on tourne dans le sens inverse. Ainsi, les **signaux  clockwise et cntclockwise** indiquent le **sens de rotation** de l'encodeur.

Enfin, selon le sens de rotation de on ajoute ou soustrait 1 à la **valeur binaire affichée** par les LEDs et on revient à 0 lorsque toutes les leds sont allumées. En pratique, on ajoute 1 quand clockwise vaut 1 et un soustrait 1 quand cntclockwise vaut 1.


## Code final:

library ieee;
use ieee.std_logic_1164.all;
use ieee.numeric_std.all; 

entity encoder is
    port (
        i_clk : in std_logic;
        i_rst_n : in  std_logic;
        i_a : in  std_logic;
	i_b : in std_logic ;
        o_leds : out std_logic_vector(3 downto 0);
	s_en : in std_logic
    );
end entity encoder;



architecture rtl of encoder is
signal  s_a_rising: std_logic :='0';
signal s_a_falling : std_logic :='0';
signal s_b_rising : std_logic :='0';
signal s_b_falling : std_logic :='0';
signal r_a_current : std_logic := '0';
signal r_b_current : std_logic := '0';
signal clockwise : std_logic := '0';
signal cnt_clockwise : std_logic := '0';
signal r_q : unsigned(3 downto 0) := (others => '0');
signal previous_a : std_logic :='0';
signal previous_b : std_logic :='0';

begin

    process(i_clk, i_rst_n,i_a) 

    begin
	if (i_rst_n = '0') then
		r_a_current <= '0';
        	
	else
		if (rising_edge(i_clk) and s_en='1') then 
			previous_a <= r_a_current;
			r_a_current <= i_a;
			previous_b <= r_b_current;
			r_b_current <= i_b;

		end if;
	end if;

    end process;
	
	s_a_rising <= '1' when( previous_a='0' and r_a_current='1' ) else'0'; 
	s_a_falling <= '1' when ( previous_a='1' and r_a_current='0' ) else'0'; 
	s_b_rising <= '1' when ( previous_a='0' and r_a_current='1' ) else'0'; 
	s_b_falling <= '1' when ( previous_a='1' and r_a_current='0' ) else'0'; 
	
    process
    begin
	if ( s_a_rising='1' and r_b_current='1') then
		cnt_clockwise <= '1';
		clockwise <= '0';
	elsif (s_b_rising= '1' and r_a_current='1') then
		cnt_clockwise <= '0';
		clockwise <= '1';
	end if;
     end process;

    process(i_clk,i_rst_n,s_en)
    

    begin
        if (i_rst_n = '0') then
            r_q <= (others => '0');
        elsif rising_edge(i_clk) then
            if (s_en = '1') then
                if (cnt_clockwise = '1' and r_q > 0) then
                    r_q <= r_q - 1; 
                elsif (clockwise = '1' and r_q < 15) then
                    r_q <= r_q + 1; 
                end if;
            end if;
        end if;
    end process;

    o_leds <= std_logic_vector(r_q);		

end architecture rtl;



## Simulation :

## Expérimentation sur la carte : 
