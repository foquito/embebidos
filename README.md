----------------------------------------------------------------------------------
-- Company: 
-- Engineer: 
-- 
-- Create Date:    10:14:05 05/23/2018 
-- Design Name: 
-- Module Name:    VGA_module - Behavioral 
-- Project Name: 
-- Target Devices: 
-- Tool versions: 
-- Description: 
--
-- Dependencies: 
--
-- Revision: 
-- Revision 0.01 - File Created
-- Additional Comments: 
--
----------------------------------------------------------------------------------
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

-- Uncomment the following library declaration if using
-- arithmetic functions with Signed or Unsigned values
--use IEEE.NUMERIC_STD.ALL;

-- Uncomment the following library declaration if instantiating
-- any Xilinx primitives in this code.
--library UNISIM;
--use UNISIM.VComponents.all;

entity VGA_module is
port( 
	clk   	 : in std_logic;
	h_sync	 : out std_logic;
	v_sync	 : out std_logic;
	red		 : out std_logic_vector(3 downto 0);
	green		 : out std_logic_vector(3 downto 0);
	blue 		 : out std_logic_vector(3 downto 0);
	new_frame : out std_logic;
	current_h : out integer range 0 to 800:=0;
	current_v : out integer range 0 to 525:=0;
	red_in	 : in std_logic_vector(3 downto 0);
	green_in  : in std_logic_vector(3 downto 0);
	blue_in	 : in std_logic_vector(3 downto 0)
);

end VGA_module;

architecture Behavioral of VGA_module is

signal h_pos : integer range 0 to 800:=0;   --FP+SYNC+BP+Visible = 16 + 96 + 48 + 640 = 800
signal v_pos : integer range 0 to 525:=0; 	--FP+SYNC+BP+Visible = 10 + 2 + 33 + 480 = 525

begin

current_h <= h_pos;
current_v <= v_pos;

vga_timing : process(clk)

begin
	if rising_edge(clk) then
		--horizontal position
		if (h_pos < 800) then
		
			new_frame <= '0';
			
			h_pos <= h_pos + 1;
		else
			h_pos <= 0;  --Reset position at end of line
			
			--vertical position
			if (v_pos < 525) then
				v_pos <= v_pos + 1;
			else 
				v_pos <= 0;  --Reset position at end of frame
				
				new_frame <= '1';
				
			end if;
		end if;
		
		--horizontal sync
		if (h_pos > 656 and h_pos < 752) then
			h_sync <= '0';
		else
			h_sync <= '1';
		end if;
		--vertical sync
		if (v_pos > 490 and v_pos < 492) then
			v_sync <= '1';
		else
			v_sync <= '0';
		end if;
		
		red <= red_in;
		green <= green_in;
		blue <= blue_in;
	
	end if;
	
end process;

end Behavioral;

