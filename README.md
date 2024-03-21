# cougarGimbalFDM
Plans for a replacement gimbal for the cougar flightstick.
This design is mostly 3D printed and reuses the original PCB.
It replaces the potentiometers with contactless AS5600 angle sensors.
![picture of assembled gimbal](/images/finishedAssembly1.jpg)

## 1 Necessary tools
- 3D printer with PETG filament
- microcontroller with i2c interface (preferably arduino)
- screwdriver for m3 torx
- file, saw for steel
- hex key 10mm
- m3 thread cutter
- soldering iron for as5600 pcbs

## 2 Bill of materials
| Part name       | minimum count | rough price estimate for reasonable order quantities |
| --------------- | ------------- | ----------------- |
| 603 ZZ bearing               | 2  | 2€     |
| 608 ZZ bearing               | 2  | 2€     |
| M6x30 galvanized hex bolt    | 4  | 0.4€   |
| M6 nut                       | 4  | < 0.1€ |
| M6 washer                    | 4  | < 0.1€ |
| M3x50 torx screw             | 19 | 3€     |
| M3 nut                       | 20 | 1€     |
| AS5600 on 15x18mm PCB        | 2  | 9€     |
| Spring, totalLen=55.5mm, wireDia=1.8mm, outerDia=9.2mm, lengthWithoutHook=45.3mm | 4 | 13€ |
| 3mm cube neodymium magnet | 2 | 0.5€ |
total cost ≈30€

## 3 Instructions for assembly
![picture of assembled gimbal](/images/cad1.PNG)
![picture of assembled gimbal](/images/i2cProgramming.jpg)
### 3.1 Printing
- print the parts inside the stl folder, some files are required multiple times as indicated by the filename
- for the "InnerPlateSide" 100% infill is recommended to reduce flex under the strong forces
- the other parts can be printed with 30% infill
- use m3 thread cutter and pretap all holes

### 3.2 Inner axis
- disassemble the original gimbal assembly, store away the screws and hex nuts from the old parts for reuse
- put a 603 bearing on a m3 bolt and secure it with a hex nut
- pass the metal part onto which the joystick screws on through the opening of the case. With the axis attached it will be difficult to pass it through afterwards.
- pass the prepared bolt through the axle hole in the original joystick metal part. Insert the bolt form the side which will later face face towards the magnet / labelplate!
- (optional) protect the wires inside with teflon tape
- on the other side add a hex nut, the second 603 bearing and again a hex nut

### 3.3 Screw plate
- insert the bolts into the plate where the springs are attached. Saw of the M3 bolts so that they do not stick out on the other side.
- you should have a assembly with four attached springs
- insert a neodymium cube into the square hole
- its north pole should NOT face towards one of the openings, otherwise the as5600 can not measure the magnet angle
- push the plate over the original metal joystick part

### 3.4 Inner gimbal assembly
- use the InnerPlateSide and push them onto the prepared inner axis assembly
- insert a neodymium cube into one of the innerPlateEnd parts, again make sure the north or south pole is not directly pointing towards the sensor
- place the innerPlateEnd part with the magnet on the correct side, such that the hall sensor cable can still reach the pcb. Basically the location where the original potentiometer was.
- assemble the inner gimbal assembly by inserting the two outer m3 screws passing through the innerPlateEnd parts

### 3.5 Outer mounting plates
- use the hex nuts saved from the original gimbal and insert the into the slots of the outer mounting plate
- the hex nuts can be inserted now, the assembly has to be done inside the metal case and can be a bit tricky
- insert the 608 bearing into opposite side and combine the pieces of the outer assembly encasing the inner gimbal assembly
- do not screw them into the case just yet

### 3.6 Hall sensors
- reuse the original wires from the potentiometers. Connect them to the 5V, GND and output pin of the AS5600 module. It will be setup to emulate a potentiometer.
- connect two wires to the i2c pins for programming. You can use thin enameled copper wire to break them off after programming.
- place the sensors into the respective plastic adapters and screw them into the gimbal. Because the screws are hard to reach the gimbal was not screwed to the case.
- After finishing the assembly the gimbal can be secured with the 8 screws from the top like original.

### 3.7 Programming the sensors
- use an arduino and connect the VCC and GND pin to the sensors cables using jumper wires. Connect the I2C pins to the correct pins on the arduino (typically A4 SDA, A5 SCL).
- install the arduino as5600 library and record the rawAngle values while checking the maximum values for each axis
- use the status register to check, that the magnet strength is in the acceptable range
- add some margin, because the original pcb can't quiet handle the full range from 0V to 5V. Probably decrease ZPOS by 30% value range and increate MPOS by 30% value range.
- WARNING!!! the programming step is irreversible and you only have 3 total attempts per sensor. The AS5600 uses one time burnable efuses for programming.
- enable the programming feature of the library by uncommenting the code inside the header and source file
- programm the range of the sensor using `as5600.setZPosition(ZPOS);`, `as5600.setMPosition(MPOS);` and `as5600.burnAngle();`, refer to the burn_zpos example
- ZPOS is the angle for which the sensor outputs 0V, MPOS is the position for 5V from the raw value.

### 3.8 spring tensioning
- file / saw a slot into the m6 boltssuch that the spring can hook into the end of the bolt and move somewhat freely
- screw on a hex nut followed by a washer, place each of the prepared screws into the corners
- attach the springs from the center assembly into the hooks
- increase the spring tension by turning the m6 nut with a 10mm wrench
