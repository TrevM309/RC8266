# RC866
ESP8266 based Remote Control system

Everything in sub projects to allow fetching only what is wanted.
PCB_8266prog directory
	Contains KiCAD files to allow creation of a single sided PCB to allow easy programming.
RC8266Controller directory
	Contains case, PCB and code to create an ESP8266 based Remote Control Controller
	Single and dual joystick controllers are included
	CaseRxCtl directory
		OpenSCAD file that can be used to create STL files to allow printing cases.
		Within RcCtl.scad
			print1() currently uncommented, produces STL for single joystick case
			comment out print1() and uncomment to get:
				battery() simple object representing battery
				pcb() object representing single joystick PCB
				pcb2() object representing dual joystick PCB
				case1() object representing a case to hold single joystick PCB
				case2() object representing a case to hold dual joystick PCB
				bung() left over, an object to fill a hole designed for accessing program port
				halfCase1() macro used for print
				stick() object representing a joystick to fit to joystick component
				halfstick() half of stick() used in printing
				print2() produces STL for printing dual joystick case
				assy1() multi coloured assembly for single joystick
				assy2() multi coloured assembly for dual joystick
					Both assy allow seeing how things slot togwether during design.
					Allows part of assembly to be removed etc.
	PCB_RcCtl
		KiCAD files to create single joystick controller PCB.
		Schematic, PCB layout and KiCAD project files here.
		Gerber files were used to get PCB created at Elecrow.
		PCB is double sided, designed so that tinned copper wire can be used for vias.
	PCB_RcCtl2
		KiCAD files to create double joystick controller PCB.
		Schematic, PCB layout and KiCAD project files here.
		Gerber files were used to get PCB created at Elecrow.
		PCB is double sided, designed so that tinned copper wire can be used for vias.
	RcCtl
		Arduino files used to program either PCB.
		Arduino setup for both is the same shown in top level setup.jpg
		common.h - DUAL defined for dual joystick PCB, commented out for single.
		lcd.cpp & h are ported accross from Sipeed Longan Nano demo code.
		wifi.cpp ssid and password must be the same in controller and device.
RC8266Device directory
	Contains PCBs and code to create ESP8266 based reveived for Remote Control models.
	Cases subdirectory
		Case will need to fit model, though a couple are created.
		Could be useful, but not certain.
	PCB_RcDev subdirectory
		KiCAD files to create both charger and receiver PCBs
		Schematic, PCB layout and KiCAD project files here.
		Gerber files were used to get PCBs created at Elecrow.
		PCBs are both double sided, designed so that tinned copper wire can be used for vias.
	RCDev subdirectory
		Arduino files used to program the receiver PCB.
		Arduino setup is shown in top level setup.jpg
		wifi.cpp ssid and password must be the same in controller and device.
		
