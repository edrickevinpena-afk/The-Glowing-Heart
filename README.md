<img width="815" height="645" alt="PCB_PCB_hEART-led_2026-04-25" src="https://github.com/user-attachments/assets/799bac85-8bb0-4857-a478-01887537cf2d" />
A handmade geometric heart-shaped frame with hidden WS2812B LED strips controlled by an ESP32-C3 Mini. An RC522 RFID reader acts as a lock — the touch sensor is completely disabled until the correct key fob is scanned. Once unlocked, the LEDs flow on one by one with a smooth water-like fade effect, then dim to darkness leaving a single glowing LED as an invitation. When touched, the whole heart flows back on and breathes gently forever. Powered by a 2S Li-ion battery with an MP1584 buck converter, the ESP32 sits in deep sleep when idle — waking only when the key fob is detected via the RFID IRQ pin. 🌹
<img width="1169" height="827" alt="Schematic_hEART-led_2026-04-25" src="https://github.com/user-attachments/assets/a276c443-b345-4dbb-974f-cd8cb8445200" />
<img width="848" height="489" alt="image" src="https://github.com/user-attachments/assets/586e7bc1-9d1f-42df-894b-d30e09c4065e" />
Im planning to conduct this design to hopefully contribute to the scientific community and surprise my partner on her birthday
1	COPPER3.5X10	+1,-1	SMD_L10.0-W3.5	2	COPPER3.5X10	null	LCSC	C9900023671	
2	1000uF	C2	SARAWUT_C1000UF	1	KM108M050J25RR0VH2FP0	CX(承兴)	LCSC	C18104	0.235
3	THREE PIN PINHEADER	LED1,TP1	THREE PIN PINHEADER	2					
4	ResistorToday	R1	FIXED RESISTOR	1	EWWR0001J20R0T9	ResistorToday	LCSC	X16151710	
5	RC522 COPY	RFID1	RFID-RC522	1					
6	JST PH2	U6	JST-PH-JST-PH2	1					
7	ESP32C3-SuperMini	U8	ESP32-C3_SUPERMINI:MODULE_ESP32-C3_SUPERMINI	1	ESP32C3-SuperMini		LCSC	C9900147397	
8	DC-DC-STEP-DOWN-MP1584	1	DC-DC-STEP-DOWN-MP1584	1					
<img width="641" height="161" alt="image" src="https://github.com/user-attachments/assets/514d3b04-9903-4624-b18c-7f2f0e5a1f85" />
