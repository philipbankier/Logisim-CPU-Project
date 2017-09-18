Logisim CPU Project

Authors: Etienne Guillot-Marquet, Philip Banks, Spencer Main 

-------------------------------------------------------------------------------
OPEN AND RUN INSTRUCTIONS:

TO OPEN: Open mainCPU.circ in Logisim Evolution, then import all other circ files in the submission folder.

TO RUN: 
1. Enable Simulate, do not enable ticks
2. Press a button on the keypad to enter a number you wish to multiply
3. Enable Ticks, it should run fine on 16hz clock speed, but I have not tested any higher

Upon running, all instructions in ROM will be dumped into RAM.
After this, the normal 13 tick cycle will commence until the program reaches a STOP, or an kill program from an error(overflow). If no error has been reached, the program will loop continuously 

-------------------------------------------------------------------------------
CYCLE TIME:
		
	ROM DUMP: 17 ticks
	
	CPU CYCLE:
		Fetch: 	6 ticks
		Instruction: 7 Ticks
-------------------------------------------------------------------------------
FORMAT:

Register bits: 0 to input/output from Register 1, 1 to input/output from Register 2
			Opcode	| Data segment										|		

1-type instructions: (Opcode begins with one, data size is 5 bits)
Load:		100 	| Register:(1bit) 	| Address:(4bits)
Store: 		101 	| Register:(1bit)	| Address:(4bits) 
BEQ: 		110		| 0 				| Address:(4bits) 
BNE: 		111 	| 0					| Address:(4bits) 

0-type instructions: (Opcode begins with a zero, data size is 4 bits)
Add:		0001	| 000				| Register:(1bit)		
Subtract: 	0010 	| 00				| (1 for r2 - r1)/(0 for r1 - r2)	| Register:(1bit)
Print: 		0011 	| 000 				| Register:(1bit)
Stop: 		0000	| 0000

NOTE: ADD only takes an output register, it assumes it's adding the numbers in register 1 + register 2

INAOFTPTW-type instructions ("I need a one for this program to work"-type instruction)
Input:		0000 	| 00				| Register:(1bit)			| 1 

NOTE: last bit is interpreted as part of opcode in this case

-------------------------------------------------------------------------------
INSTRUCTION WRITING TO ROM:
	Custom instructions/data are hard wirted in ROM. Open the "ROM16byte" and wire the instructions.
	The format is 4 collumns of 4 splitter's. Each splitter connects to 8 bits, with the LSB at the bottom and the MSB at the top of the splitter. 
	Addresses are labled.

-------------------------------------------------------------------------------
MULTIPLY PROGRAM:
Address	Instruction				Instruction in binary

0000	Input: 	R1 				00000001
0001	Store: 	R1 -> 0001		10100001				
0010	Load:	0001 -> R1		10000001
0011	Load:	1111 -> R2		10011111
0100	Add: 	R2 = R2 + R1	00010001
0101	Store:	R2 -> 1111		10111111
0110	Print: 	R2 -> Display	00110010
0111	Load:	1110 -> R2		10011110
1000	Load:	1 -> R1			10000000
1001	Sub: 	R2 = R2 - R1	00100011
1010	Load: 	0 -> R1			10001101
1011	Store	R2 -> 1110		10111110
1100	BNEQ:	R2,R1,0010		11100010
1101	STOP:					00000000
1110	Integer Data:			Some binary number, for the purpose of testing we input 3 (00000011)
1111	Ouput Data: 			00000000

Notes and bugs:
- Currently the display does not work, the output bits are sent to a buffer
