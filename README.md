<img width="815" height="645" alt="PCB_PCB_hEART-led_2026-04-25" src="https://github.com/user-attachments/assets/799bac85-8bb0-4857-a478-01887537cf2d" />
This is a project of mine inspired by my desire to explore the electronics world and my passion for it.  I am 17 years old and I  designed the system myself.This project entitled 'The Glowing Heart' is done to hopefuly surprise my partner on her birthday. I plan to make it as heartwarming as possible, by including two LED strips ina shape of a heart that can b controlled by a microcontroller(esp32 c3 mini), adding rfid module, touch sensor, 2s li ion battery pack, and a charger module. Now the system is  designed and coded in  such a way that the rfid card will  wake the esp32 c3 mini  from its deep  sleep(to conserve power, potentially it can last for more than 6 months) and the led strips will light up once like a wave. Then one led from the strips are left glowing, hinting the touch sensor. The system will have five modes,one touch will activate the next and so on until it reaches off(5th function). A LCD Display anda speaker will   be added to  increase the volume of the design, the LCD will  display the steps needed for the flow and a short heartbounded message will  appear once activated.  The speaker will  be the one to add emotiional depth too the scene.
1 tap Cycle to next color

2 taps Cycle to previous color

3 tapsChange LED effect

Hold (2sec) Dim/brighten toggle

Double tap Turn off/on

A battery charger will be used to elongate the lifespan of the glowing heart.

<img width="556" height="425" alt="image" src="https://github.com/user-attachments/assets/02c68523-bce5-4556-a3b1-04bba696e894" />
<img width="304" height="281" alt="image" src="https://github.com/user-attachments/assets/07eab2c8-b7f5-4932-bf15-75538dd5d0e6" />
<img width="612" height="485" alt="image" src="https://github.com/user-attachments/assets/9eddc347-0bfe-4d0e-83e2-44dd8d881c1e" />

1	COPPER3.5X10	+1,-1	SMD_L10.0-W3.5	2	COPPER3.5X10	null	LCSC	C9900023671	
2	1000uF	C2	SARAWUT_C1000UF	1	KM108M050J25RR0VH2FP0	CX(承兴)	LCSC	C18104	0.235
3	THREE PIN PINHEADER	LED1,TP1	THREE PIN PINHEADER	2					
4	ResistorToday	R1	FIXED RESISTOR	1	EWWR0001J20R0T9	ResistorToday	LCSC	X16151710	
5	RC522 COPY	RFID1	RFID-RC522	1					
6	JST PH2	U6	JST-PH-JST-PH2	1					
7	ESP32C3-SuperMini	U8	ESP32-C3_SUPERMINI:MODULE_ESP32-C3_SUPERMINI	1	ESP32C3-SuperMini		LCSC	C9900147397	
8	DC-DC-STEP-DOWN-MP1584	1	DC-DC-STEP-DOWN-MP1584	1					
<img width="641" height="161" alt="image" src="https://github.com/user-attachments/assets/514d3b04-9903-4624-b18c-7f2f0e5a1f85" />
