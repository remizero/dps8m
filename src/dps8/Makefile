# Copyright 2013-2016 by Harry Reed
# Copyright 2013-2016 by Charles Anthony
# Copyright 2016 Jean-Michel Merliot
# Copyright 2016 by Michal Tomek
#
# All rights reserved.
#
# This software is made available under the terms of the
# ICU License -- ICU 1.8.1 and later. 
# See the LICENSE file at the top-level directory of this distribution and
# at https://sourceforge.net/p/dps8m/code/ci/master/tree/LICENSE

#
# Condition builds
#
#  TESTING=1    Enables sim_debug and additional runtime checks
#

all :

SIMHx = ../simh-master

INSTALL_ROOT ?= /usr/local/
INSTALL_BIN ?= $(INSTALL_ROOT)/bin


include ../Makefile.mk

ifneq (,$(wildcard Makefile.local))
$(warning ####)
$(warning #### Using Makefile.local)
$(warning ####)
include Makefile.local
endif

ifdef TESTING
$(warning ####)
$(warning #### TESTING mode)
$(warning ####)
CFLAGS += -DTESTING
endif

#ifeq ($(LOADER),1)
CFLAGS += -DLOADER
#endif

ifeq ($(OS),Windows_NT)
CROSS=MINGW64
endif

ifeq ($(THREADZ),1)
CFLAGS += -pthread -DTHREADZ
LDFLAGS += -pthread
LIBS += -lpthread
endif

ifeq ($(LOCKLESS),1)
#CFLAGS += -pthread -DLOCKLESS -DWAM
CFLAGS += -pthread -DLOCKLESS 
LDFLAGS += -pthread
LIBS += -lpthread
endif

ifndef LIBUV
LIBUV = -luv
endif

# FNP2 support
LIBS += $(LIBUV)

ifeq ($(CROSS),MINGW64)
CFLAGS += -DCROSS_MINGW64 -I../../../libuv/include
ifneq ($(W),)
CFLAGS += -Wno-misleading-indentation
endif
LIBS += -lwinmm -lws2_32 -lmingwex -luv -lwsock32 -lws2_32 -L../../../libuv/.libs
endif

CFLAGS += -I../decNumber -I$(SIMHx)
#ifeq ($(CROSS),MINGW64)
#CFLAGS += -I../gnulib
#endif

# gcc is reporting completely bogus 'conversion' warnings.

ifneq ($(W),)
ifeq ($(CC),clang)
CFLAGS += -Wall -Wconversion -Wsign-conversion
else
CFLAGS += -Wall -Wno-conversion -Wsign-conversion
endif
endif

ifneq ($(M32),)
CFLAGS += -DNEED_128
ifneq ($(W),)
CFLAGS += -Wno-format-zero-length -Wno-sign-conversion
endif
endif

# The system_state code relies on M_SHARED.
ifneq ($(CROSS),MINGW64)
CFLAGS += -DM_SHARED
ifneq ($(OS),OSX)
LDFLAGS += -lrt
endif
endif

# Configuration switches

ifeq ($(CROSS),MINGW64)
LOCALLIBS += $(SIMHx)/simh.a ../decNumber/decNumber.a 
#../gnulib/gnulib.a
else
LOCALLIBS += $(SIMHx)/simh.a ../decNumber/decNumber.a 
endif

# simh requires -ldl
# Cygwin seems to need...
ifeq ($(OS),Windows_NT)
    LIBS += -lgcc -lm  -lwinmm -lws2_32 -lmingwex $(LIBUV) -lwsock32 -lpthread -lgnurx -lpsapi -liphlpapi  -luserenv
else
  OSTYPE = $(shell uname -s)
  ifeq ($(OSTYPE),FreeBSD)
    LIBS += -lm -pthread
  endif
  ifeq ($(OSTYPE),Linux)
    ifeq ($(CROSS),MINGW64)
      LIBS += -lm
    else
      LIBS += -lm -ldl
    endif
  endif
endif
#LIBS += -lm -ldl

# efence is Electric Fence, a malloc debugger.
#LIBS +- -lefence

# link in gperftools 
#LIBS += -lprofiler

ifneq ($(CROSS),MINGW64)
C_SRCS  = ./dps8_absi.c
endif
C_SRCS += ./dps8_addrmods.c 
C_SRCS += ./dps8_append.c
C_SRCS += ./dps8_cable.c
C_SRCS += ./dps8_console.c
C_SRCS += ./dps8_cpu.c
C_SRCS += ./dps8_crdpun.c
C_SRCS += ./dps8_crdrdr.c
C_SRCS += ./dps8_decimal.c
C_SRCS += ./dps8_disk.c
C_SRCS += ./dps8_eis.c
C_SRCS += ./dps8_faults.c
C_SRCS += ./dps8_fnp2.c
C_SRCS += ./dps8_fnp2_iomcmd.c
C_SRCS += ./dps8_iefp.c
C_SRCS += ./dps8_ins.c
C_SRCS += ./dps8_iom.c
#ifeq ($(LOADER),1)
C_SRCS += ./dps8_loader.c
#endif
C_SRCS += ./dps8_math.c
C_SRCS += ./dps8_math128.c
C_SRCS += ./dps8_mt.c
C_SRCS += ./dps8_opcodetable.c
C_SRCS += ./dps8_prt.c
C_SRCS += ./dps8_scu.c
C_SRCS += ./dps8_simh.c
ifneq ($(CROSS),MINGW64)
C_SRCS += ./dps8_socket_dev.c
endif
C_SRCS += ./dps8_sys.c
C_SRCS += ./dps8_urp.c
C_SRCS += ./dps8_utils.c
C_SRCS += ./fnpuv.c
C_SRCS += ./fnptelnet.c
C_SRCS += libtelnet.c
C_SRCS += ./hdbg.c
# The system_state code relies on M_SHARED.
ifneq ($(CROSS),MINGW64)
#ifeq ($(M_SHARED),1)
C_SRCS += ./shm.c
#endif
endif
ifneq ($(CROSS),MINGW64)
C_SRCS += ./udplib.c
endif
ifeq ($(THREADZ),1)
C_SRCS += ./threadz.c
endif
ifeq ($(LOCKLESS),1)
C_SRCS += ./threadz.c
endif
C_SRCS += ./uvutil.c

OBJS  := $(patsubst %.c,%.o,$(C_SRCS))

ifneq ($(CROSS),MINGW64)
H_SRCS  = dps8_absi.h
endif
H_SRCS += dps8_addrmods.h
H_SRCS += dps8_append.h
H_SRCS += dps8_cable.h
H_SRCS += dps8_console.h
H_SRCS += dps8_cpu.h
H_SRCS += dps8_crdpun.h
H_SRCS += dps8_crdrdr.h
H_SRCS += dps8_decimal.h
H_SRCS += dps8_disk.h
H_SRCS += dps8_eis.h
H_SRCS += dps8_em_consts.h
H_SRCS += dps8_faults.h
H_SRCS += dps8_fnp2.h
H_SRCS += dps8_fnp2_iomcmd.h
H_SRCS += libtelnet.h
H_SRCS += dps8.h
H_SRCS += dps8_hw_consts.h
H_SRCS += dps8_iefp.h
H_SRCS += dps8_ins.h
H_SRCS += dps8_iom.h
#ifeq ($(LOADER),1)
H_SRCS += dps8_loader.h
#endif
H_SRCS += dps8_math.h
H_SRCS += dps8_math128.h
#H_SRCS += dps8_mp.h
H_SRCS += dps8_mt.h
H_SRCS += dps8_opcodetable.h
H_SRCS += dps8_prt.h
H_SRCS += dps8_socket_dev.h
H_SRCS += dps8_scu.h
H_SRCS += dps8_simh.h
H_SRCS += dps8_state.h
H_SRCS += dps8_sys.h
H_SRCS += dps8_urp.h
H_SRCS += dps8_utils.h
H_SRCS += fnpuv.h
H_SRCS += fnptelnet.h
H_SRCS += h316_imp.h
H_SRCS += hdbg.h
# The system_state code relies on M_SHARED.
#ifeq ($(M_SHARED),1)
H_SRCS += shm.h
#endif
ifneq ($(CROSS),MINGW64)
H_SRCS += udplib.h
endif
H_SRCS += utarray.h
H_SRCS += uthash.h
H_SRCS += utlist.h
ifeq ($(THREADZ),1)
H_SRCS += ./threadz.h
endif
ifeq ($(LOCKLESS),1)
H_SRCS += ./threadz.h
endif
H_SRCS += ./uvutil.h



all: utils $(TAGS) locallibs_then_dps8

#fnpx : fnpx.c fnp_udplib.o
#	$(CC) fnpx.c -o fnpx fnp_udplib.o

#remote : remote.c fnp_udplib.o
#	$(CC) remote.c -o remote fnp_udplib.o

install: all
	cp dps8$(EXE) $(INSTALL_BIN)/dps8$(EXE)

cscope:
	cscope $(C_SRCS)

android : 
	ndk-build APP_BUILD_SCRIPT=./Android.mk APP_ABI=arm64-v8a APP_PLATFORM=android-21

blinkenLights : blinkenLights.c shm.o
	@echo LD blinkenLights
	@$(CC) $(CFLAGS) $(LDFLAGS) blinkenLights.c -o blinkenLights shm.o `pkg-config --cflags --libs gtk+-3.0`

blinkenLights2 : blinkenLights2.c shm.o
	$(CC) $(CFLAGS) $(LDFLAGS) blinkenLights2.c -o blinkenLights2 shm.o `pkg-config --cflags --libs gtk+-3.0` -lrt

blinkenLightsPi : blinkenLightsPi.c shm.o
	$(CC) $(CFLAGS) $(LDFLAGS) blinkenLightsPi.c -o blinkenLightsPi shm.o 

#all : blinkenLights

blinkenLightsCurses : blinkenLightsCurses.c shm.o
	@echo LD blinkenLightsCurses
	@$(CC) $(CFLAGS) $(LDFLAGS) blinkenLightsCurses.c -o blinkenLightsCurses shm.o -lncurses

#all : blinkenLightsCurses

dps8$(EXE) : $(OBJS) $(LOCALOBJS) $(LOCALLIBS) 
	@echo LD dps8$(EXE)
	@$(LD)  -o dps8$(EXE) $(LDFLAGS) $(OBJS) $(LOCALOBJS) $(LOCALLIBS) $(LIBS)

.PHONY: FORCE

ifeq (,$(wildcard ../../.git))
# not git
ifeq (,$(wildcard dps8.sha1.txt~))
# not git, not kit
dps8.sha1.txt : 
	echo '#define COMMIT_ID "unknown"' > dps8.sha1.txt
else
# kit
dps8.sha1.txt : 
	cp dps8.sha1.txt~ dps8.sha1.txt
endif
else
# git
dps8.sha1.txt : FORCE
	@echo '#define COMMIT_ID "unknown"' > dps8.sha1.txt~
	@-git rev-parse HEAD > /dev/null 2>&1 && git rev-parse HEAD | awk '{print "#define COMMIT_ID \""$$1"\""}' > dps8.sha1.txt~
	@if [ -e dps8.sha1.txt ] ; then \
	    if diff dps8.sha1.txt dps8.sha1.txt~ >/dev/null ; then \
	        rm dps8.sha1.txt~ ; \
	    else \
	        mv dps8.sha1.txt~ dps8.sha1.txt ; echo 'Generated dps8.sha1.txt' ; \
	    fi \
	else \
	    mv dps8.sha1.txt~ dps8.sha1.txt ; echo 'Generated dps8.sha1.txt' ; \
	fi
endif


slte.inc : system_book_12_3.ascii
	./buildSLTE.sh

locallibs_then_dps8 : 
	(cd $(SIMHx); $(MAKE) -f Makefile)
	(cd ../decNumber; $(MAKE))
ifeq ($(CROSS),MINGW64)
	#(cd ../gnulib; $(MAKE))
endif
	#$(MAKE) zmq
	$(MAKE) dps8.sha1.txt
	$(MAKE) dps8$(EXE)


# Other Targets

utils :
	@$(MAKE) -C ../utils 

udplib : udplib.c udplib.h
	clang udplib.c -o udplib -DTEST=1

.PHONY: zeromq czmq zyre 

zmq: zeromq czmq zyre

zeromq : ../lib/libzmq.a

../lib/libzmq.a :
	(cd ../zeromq-4.1.0 && ./configure --prefix=`pwd`/../ && $(MAKE) && $(MAKE) check && $(MAKE) install)
	
czmq : ../lib/libczmq.so

../lib/libczmq.so :
	(cd ../czmq-master-ff0d028b16 && cmake . -DZEROMQ_LIBRARIES=../lib/libzmq.so -DZEROMQ_INCLUDE_DIRS=../include/ -DCMAKE_INSTALL_PREFIX=.. && $(MAKE) && $(MAKE) install)

zyre : ../lib/libzyre.so

../lib/libzyre.so :
	(cd ../zyre-master-9d7e62d2c9 && cmake . -DCZMQ_LIBRARIES=../lib/libczmq.so -DCZMQ_INCLUDE_DIRS=../include -DZEROMQ_LIBRARIES=../lib/libzmq.so -DZEROMQ_INCLUDE_DIRS=../include/ -DCMAKE_INSTALL_PREFIX=.. && $(MAKE) && $(MAKE) install)

# If you do not have ctags in your build environment, change 'ctags' to 
# '#ctags' below.

tags : $(C_SRCS) $(H_SRCS) dps8.sha1.txt
	@echo TAGS
	-@ctags $(C_SRCS) $(H_SRCS) $(SIMHx)/*.[ch] ../decNumber/dec*.[ch]

.PHONY : tapes

tapes: 88534.tap 88631.tap 88632.tap 88633.tap 88634.tap 88635.tap 88636.tap 99020.tap 93085.tap 98570.tap 99019.tap 98580_1er.tap 98576_1er.tap 98577.tap 20184.tap 20185.tap 20186.tap 20187.tap 20188.tap t4d_b.2.tap

%.tap:
	wget bitsavers.trailing-edge.com/bits/Honeywell/multics/tape/$@.gz
	gunzip $@.gz

.PHONY : clean 

clean:
	@echo CLEAN
	@$(MAKE) -C $(SIMHx) -f Makefile clean
	#(cd ../decNumber; $(MAKE) clean)
	@$(MAKE) -C ../decNumber clean
	@$(MAKE) -C ../utils clean
	@$(MAKE) clean_dps8

.PHONY: clean_dps8

clean_dps8:
	@echo CLEAN dps8
	-@$(RM) $(OBJS) $(C_DEPS) $(EXECUTABLES) dps8$(EXE) tags $(C_SRCS:.c=.d) $(wildcard $(C_SRCS:.c=.d.[0-9]*)) dps8.sha1.txt

dps8_sys.o : dps8.sha1.txt
dps8_sys.d : dps8.sha1.txt

dps8_socket_dev.c: errnos.h
errnos.h:
	echo '#include <errno.h>' > dump.c
	$(CC) -E -dD dump.c | awk '/^#define E/ { printf "{\"%s\",%s},\n", $$2, $$2 }' > errnos.h
	rm -f dump.c

%.d: %.c
	@set -e; rm -f $@; \
	$(CC) -MM $(CFLAGS) $< > $@.$$$$; \
	grep -v dps8.sha1.txt $@.$$$$ | sed 's,\($*\)\.o[ :]*,\1.o $@ : ,g' > $@; \
	rm -f $@.$$$$

-include $(C_SRCS:.c=.d)
