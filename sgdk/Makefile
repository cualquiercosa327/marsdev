MARSBIN  = $(MARSDEV)/m68k-elf/bin
TOOLSBIN = $(MARSDEV)/bin

BINTOS  = $(TOOLSBIN)/bintos
RESCOMP = $(TOOLSBIN)/rescomp
XGMTOOL = $(TOOLSBIN)/xgmtool
WAVTORAW= $(TOOLSBIN)/wavtoraw
PCMTORAW= $(TOOLSBIN)/pcmtoraw
SIZEBND = $(TOOLSBIN)/sizebnd
LZ4W    = $(TOOLSBIN)/lz4w.jar

LIBMD   = $(MARSDEV)/m68k-elf/lib/libmd.a

SGDK_VER ?= master

.PHONY: all tools clean

all: SGDK tools $(LIBMD)

tools: $(BINTOS) $(RESCOMP) $(XGMTOOL) $(WAVTORAW) $(PCMTORAW) $(SIZEBND) $(LZ4W)

# OSX sed fails without the '', and GNU fails with, cool beans
SED_FIX=
ifeq ($(shell uname -s),Darwin)
	SED_FIX=''
endif

$(LIBMD):
	# Compress resources with lz4w instead of aplib
	sed -i  $(SED_FIX) 's/-1/2/g' SGDK/res/libres.res
	cp -f Makefile.libmd SGDK/Makefile
	make -C SGDK
	cp -f SGDK/libmd.a $@
	cp -f SGDK/inc/*.h $(MARSDEV)/m68k-elf/include/
	cp -f SGDK/res/*.h $(MARSDEV)/m68k-elf/include/
	
SGDK:
	git clone --branch $(SGDK_VER) https://github.com/Stephane-D/SGDK

# Tools

$(BINTOS):
	gcc SGDK/tools/bintos/src/bintos.c -o $@
	
$(RESCOMP):
	if [ -f "SGDK/bin/rescomp.jar" ]; then \
		cp -f SGDK/bin/rescomp.jar $(TOOLSBIN)/rescomp.jar ; \
	else \
		cd SGDK/tools/rescomp/src && gcc *.c -o $(TOOLSBIN)/rescomp ; \
	fi
	
$(XGMTOOL):
	cd SGDK/tools/xgmtool/src && gcc *.c -I../inc -lm -o $@
	
$(WAVTORAW):
	if [ -d "SGDK/tools/wavtoraw"]; then \
		gcc SGDK/tools/wavtoraw/src/wavtoraw.c -lm -o $@ ; \
	fi
	
$(PCMTORAW):
	if [ -d "SGDK/tools/pcmtoraw"]; then \
		gcc SGDK/tools/pcmtoraw/src/pcmtoraw.c -lm -o $@ ; \
	fi
	
$(SIZEBND):
	gcc SGDK/tools/sizebnd/src/sizebnd.c -o $@

$(LZ4W):
	cp -f SGDK/bin/lz4w.jar $@

clean:
	rm -rf SGDK
