CC=gcc
CFLAGS=-c -Wall -Wextra -Wpedantic -Wno-unused-parameter -Wno-cast-function-type -O3 -s -flto -ffunction-sections -fdata-sections -DNDEBUG
LDFLAGS=-O3 -s -flto -Wl,--gc-sections
SOURCES=main.c
INCLUDE_DIRS=-I .
CHECK=1

ifeq ($(OS),Windows_NT)
	SOURCES+=hid-win.c
	SOURCES+=rs232.c
	LIBS=-lsetupapi -lhid
else
	UNAME_S := $(shell uname -s)
	ifeq ($(UNAME_S),Darwin)
		SOURCES+=hid-mac.c
		SOURCES+=rs232.c
		LIBS=-framework IOKit -framework CoreFoundation
	else
		CHECK:=$(shell pkg-config --exists libusb-1.0 && echo 1)
		SOURCES+=hid-libusb.c
		SOURCES+=rs232.c
		LIBS=`pkg-config libusb-1.0 --libs` -lrt -lpthread
		INCLUDE_DIRS+=`pkg-config libusb-1.0 --cflags`
		CFLAGS+=-std=gnu99
		LDFLAGS+=-no-pie
	endif
endif

OBJECTS=$(SOURCES:.c=.o)

EXECUTABLE = hid-flash

ifeq ($(CHECK), 1)
all: $(SOURCES) $(EXECUTABLE)

$(EXECUTABLE): $(OBJECTS)
	$(CC) $(LDFLAGS) $(OBJECTS) $(LIBS) -o $@

.c.o:
	$(CC) $(CFLAGS) $(INCLUDE_DIRS) $< -o $@
else
all:
	@echo "    hid-flash requires libusb-1.0, please install with:"
	@echo "    sudo apt-get install libusb-1.0"
endif

clean:
	rm -f $(OBJECTS) $(EXECUTABLE) $(EXECUTABLE).exe
