####
#### Sample Makefile for building apps with the RIOT OS
####
#### The Sample Filesystem Layout is:
#### /this Makefile
#### ../../RIOT 
#### ../../RIOT/board   for board definitions (if you have one or more)
#### 

#### Mandatory vars!

# this has to be the absolute path of the RIOT-base dir
export RIOTBASE =$(CURDIR)/../../RIOT
export RIOTBOARD =$(RIOTBASE)/board

# the cpu to build for
export CPU = msp430x16x
export MCU = msp430x1612

# toolchain config
export PREFIX = @msp430-
export CC = @$(PREFIX)gcc
export AR = @$(PREFIX)ar
export CFLAGS += -std=gnu99 -Wstrict-prototypes -gdwarf-2 -Os -Wall -mmcu=$(MCU)
export ASFLAGS += -mmcu=$(MCU) --defsym $(MCU)=1 --gdwarf-2
export AS = $(PREFIX)as
export LINK = $(PREFIX)gcc
export SIZE = $(PREFIX)size
export OBJCOPY = $(PREFIX)objcopy
TERM = pyterm.py
LINKFLAGS = -mmcu=$(MCU) -lgcc $(RIOTBASE)/bin/startup.o

PROJBINDIR = bin

ifeq ($(strip $(PORT)),)
	PORT = /dev/ttyUSB0
endif

## Modules to include. No autodependencies!

#USEMODULE += nanopan5375_proprietary
#USEMODULE += cmdd
#USEMODULE += logd
#USEMODULE += mprint
#USEMODULE += mqueue
#USEMODULE += sync_read
#USEMODULE += sysmon
#USEMODULE += syslog
#USEMODULE += tracelog
USEMODULE += auto_init
#USEMODULE += lib 
USEMODULE += shell
USEMODULE += shell_commands
USEMODULE += uart0
USEMODULE += vtimer
USEMODULE += sht11
#USEMODULE += powermon
#USEMODULE += ltc4150
USEMODULE += ps
#USEMODULE += sys_config
#USEMODULE += gpioint
USEMODULE += cc110x_ng
USEMODULE += transceiver
#USEMODULE += rtc
#USEMODULE += mci
USEMODULE += posix
USEMODULE += config
#USEMODULE += cmdengine
#USEMODULE += ezxml
#USEMODULE += fat
#USEMODULE += gps
#USEMODULE += geo
#USEMODULE += gps_conversion
#USEMODULE += gps_ublox
#USEMODULE += net_kernel
#USEMODULE += net_mm

#### Project Config 

# name of your project
PROJECT = default

# for easy switching of boards
export BOARD = msb-430h

# mandatory include! 
include $(RIOTBASE)/Makefile.modules 

# if you want to publish the board into the sources as an uppercase #define
BB = $(shell echo $(BOARD)|tr 'a-z' 'A-Z')
CFLAGS += -DBOARD=$(BB)
export CFLAGS

# your binaries to link
BASELIBS += $(RIOTBOARD)/$(BOARD)/bin/$(BOARD)_base.a
BASELIBS += $(PROJBINDIR)/project.a

INCLUDES = -I$(RIOTBOARD)/$(BOARD)/include -I$(RIOTBASE)/core/include -I$(RIOTBASE)/cpu/$(CPU)/include -I$(RIOTBASE)/sys/lib -I$(RIOTBASE)/sys/include/ -I$(RIOTBASE)/drivers/include/

export INCLUDES

## make script for your project. Build RIOT-base here!
all: $(PROJBINDIR)/$(PROJECT).a
	@echo "Building project $(PROJECT) for $(BOARD) w/ MCU $(CPU)."
	$(MAKE) -C $(RIOTBOARD)
	$(MAKE) -C $(RIOTBASE)
	$(LINK) $(LINKFLAGS) $(UNDEF) -o $(PROJBINDIR)/$(PROJECT).elf -Wl,--start-group $(BASELIBS) -lm -Wl,--end-group  -Wl,-Map=$(PROJBINDIR)/$(PROJECT).map
	$(SIZE) $(PROJBINDIR)/$(PROJECT).elf
	$(OBJCOPY) -O ihex $(PROJBINDIR)/$(PROJECT).elf $(PROJBINDIR)/$(PROJECT).hex

## your make rules
## Only basic example - modify it for larger projects!!

$(PROJBINDIR)/$(PROJECT).a: $(PROJBINDIR)/$(PROJECT).o
	$(AR) -rc $(PROJBINDIR)/project.a $(PROJBINDIR)/$(PROJECT).o 
	
$(PROJBINDIR)/$(PROJECT).o: main.c 
	$(CC) $(CFLAGS) $(BOARDINCLUDE) $(INCLUDES) -c main.c -o $(PROJBINDIR)/$(PROJECT).o

clean:
	$(MAKE) -C $(RIOTBOARD) clean
	$(MAKE) -C $(RIOTBASE) clean
	rm -f $(PROJBINDIR)/*

flash: all
	$(FLASH) $(PORT) $(PROJBINDIR)/$(PROJECT).hex

term:
	$(TERM) $(PORT)

doc:
	make -BC $(RIOTBASE) doc