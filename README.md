# volvoextradisp
Extra display to my volvo

In my 2013 Volvo I do miss some information in the original instrument cluster. Such as temperature of Engine.
Reading in VIDA in says that display will show me if the temperature is to high.
So the value itself has to be on the CANbus, so start to digging into the cantraffic on the 
midspeed network gave me some information. I just hijacked the the CAN network behind the midcenter placed display. (It just have 4 wires, 12V, GND, CANL, CANH, so the "key on" or 15 Signal has to be on the network)

This is just a very rough estimation. May not be correct.

ID 0x155, DL8, data d5 d6 - RPM 16bit unsigned.
ID 0x155, DL8, D1 - Status (bit 3 - Ignition on?) 	
ID 0x155, DL8, D2 - Status (bit 6 - Engine startup)?
ID 0x155, DL8, D2 - Status (bit 3 - Engine running)?
ID 0x145, DL8, data d7 d8 - Speed 16bit unsigned. (Speed * 100) (Gets deci speed) (Backup Speed on D5 D6)

ID 0x195 D5 D6, D7 D8
0x145 Speed, 2 different

0x4C5	Bit 6, CLT		- Coolant in C degrees, offset 60, so 80 is 20 degree C.

ID 0x035 D1: On? 0x04 = Ignition ON 

Structure used to get data from canbus and convert to a real unit:

volvo_id_s  rpm {.adress=0x155, .data_offset=4, .data_length=2, .multiplier=1.0, .value_offset=0};    
//Just raw value of RPM, same as the real RPM.

volvo_id_s  speed {.adress=0x145, .data_offset=6, .data_length=2, .multiplier=0.1, .value_offset=0};  
//There is two speed signals in same can frame, one from gearbox and the second from ABS system. Values in deci Km/h.

volvo_id_s  clt {.adress=0x4C5, .data_offset=5, .data_length=1, .multiplier=1.5, .value_offset=60};  
//Kind of rough values, precision of 1,5 degree. 


sort.sh:

Will be used to sort data from .trc files.

Usage:
./sort input.file canid byteinframe output.file 

This will also plot files with gnuplot, settings in gnuplotconf

This sort functions will only look at 1 byte.
To be able to grab two bytes, use sort16.sh, otherwise the same as sort.sh


Added arduino code for Teensy with CAN shield:

Just some example code, not nearly finished yet! Havent even tried it in the car yet.

2016-06-15	-	 Update

Now working code for temp reading.
Code still rough but working. 
Next step is to find Key ON signal, to be able to shut display down when car is not running.



