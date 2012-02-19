
# the name of the main file and sources to be used
APPNAME=serial_echo
SOURCES=$(APPNAME).o

# CPU type as defined by gcc and AVR-DUDE
GCC_MMCU=atmega1280
AVRDUDE_MCU=m1280
#GCC_MMCU=atmega328p
#AVRDUDE_MCU=atmega328p

# CPU Clock speed (cycles per second)
CLOCKSPEED=16000000
#CLOCKSPEED=8000000

# port the arduino is connected to
PORT=/dev/ttyACM0
# PORT=/dev/ttyUSB0

CC=avr-gcc
CXX=avr-g++

ARDUINO_SOURCES_DIR=/usr/share/arduino/hardware/arduino/cores/arduino

SHAREDFLAGS= -gstabs -Os -Wall \
		-funsigned-char -funsigned-bitfields -fpack-struct -fshort-enums \
		-I$(ARDUINO_SOURCES_DIR) -mmcu=$(GCC_MMCU) -DF_CPU=$(CLOCKSPEED)

CFLAGS=-std=gnu99 -Wstrict-prototypes $(SHAREDFLAGS)
CXXFLAGS=$(SHAREDFLAGS)

ARDUINOSOURCES= HardwareSerial.o \
			pins_arduino.o \
			Print.o \
			Tone.o \
			WInterrupts.o \
			wiring_analog.o \
			wiring.o \
			wiring_digital.o \
			wiring_pulse.o \
			wiring_shift.o \
			WMath.o \
			WString.o


%.o : %.c
	$(CC) $(CFLAGS) -c $< -o $@

%.o : %.cpp
	$(CXX) $(CXXFLAGS) -c $< -o $@

%.o : $(ARDUINO_SOURCES_DIR)/%.c
	$(CC) $(CFLAGS) -c $< -o $@

%.o : $(ARDUINO_SOURCES_DIR)/%.cpp
	$(CXX) $(CXXFLAGS) -c $< -o $@

all: $(APPNAME).hex

clean:
	rm -f *.o *.o *.hex

upload: $(APPNAME).hex
	stty -F $(PORT) hupcl # e.g. reset the arduino
	avrdude -v -c stk500v1 -p $(AVRDUDE_MCU) \
		-b 57600 -P $(PORT) -U flash:w:$(APPNAME).hex

%.hex : %
	avr-objcopy -O ihex -R .eeprom $< $@

libarduinocore.a: $(ARDUINOSOURCES)
	ar rc $@ $^

$(APPNAME) : $(SOURCES) libarduinocore.a
	$(CXX) $(CXXFLAGS) $^ -o $@ -L. -larduinocore \
	&& echo YAY || echo "DARN $$?"
