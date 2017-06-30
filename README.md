# MSO
Web-based Mixed Signal Oscilloscope to use for example with RedPitaya StemLab

![Screenshot](screenshot.png)



parameters :

	DIGITALNAME :list of name for digital signal if void prefix is used format: DIGITAL_XXXX 'DIGITAL' is keyword, '_' is separator 'xxxx' is prefix name,
				(ex: "DATA" name 8 traces 'DATA0..DATA7).

	SAMPLERATE : frequence of sampling (ex: 125000000).

	TRACELIST : used for bus definition(ex: {"MBUS":{"Baseband":1,"PulsePattern":8,"ModulationState":3,"Sequence":3,"Bit":2,"Byte":8}})
			is json definition of bus format object

	DATASTART : start point of request data block.

	DATASIZE : size of request data block if > 1000 datablock are downsampled ,
			default size are 1000 pts.
			if datasize <= 1000 return 1000 points 1D array of value
			else return 1000 points 1D array interlaced, point, value, point, value.....


in_command :

	GET_REF : get ref buffer trace downsampled to 1000 pts

signals: // maybe use signals as parameters to avoid signals update every 20ms

	DIGITAL_xxxx : digital compressed trace 8bits, but for downsampled data point value require double, double a used for not dowsampled data

	ANALOG_xxxx : analog signal (double)
	
	// the last one send are on top of the graph

MBUS: no downsampling, update size of server buffer or downsample bus by bus and mux all before to send.
	format definition is json string parameter "TRACELIST" [{"MBUS":{"Baseband":1,"PulsePattern":8,"ModulationState":3,"Sequence":3,"Bit":2,"Byte":8}}]
	in order of bus bit used for example this bus = 
        
				bit  0  	Baseband 1 bit (this is single signal display as DIGITAL type
				bit  1 -> 8	PulsePattern 8 bits bus
				bit  9 -> 11	ModulationState 3 bits bus
				bit 12 -> 14	Sequence 3 bits bus
				bit 15 -> 16	Bit 2 bits bus
				bit 17 -> 24	Byte 8 bits bus

send packet in format similar to VCD [first point x, first data,......,last point x,last point value]
	the first and last point must be calulated and inserted in data array according to datastart and datasize

	for example : if vcd data first change at (250*8)ns value = 01 and request block are datastart = 0 datasize = 1000
	first point = 0,0  next point =  250,1 last point = 1000,1 
	data = [0,0,250,1,1000,1]
	draw bus canvas from 0 to 250 text = 0x00 and 	250 to 1000 text = 01
				<_____0x00_____><_____0x01_______>				 
					
