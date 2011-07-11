# ===============================================
# Makefile for VirtualT.  Notice that the order
# of the fltk_images and fltk libraries in the
# LIBFILES definition is order dependent.
#
# This Makefile builds silently except to echo
# the current compile file.  To see the actual
# build commands, comment out the .SILENT:
# line below.
# ===============================================
.SILENT:

-include $(shell uname).mk

CFLAGS		+=	 -I src/FLU -O2
CPPFLAGS	+=	 -I src -O2
EXECUTABLE	=	virtualt
CLIENT		=	vt_client

# =============================
# Find the FLTK libs
# =============================
FLTKCONFIG  =   $(shell which fltk-config)
ifneq ($(FLTKCONFIG),)
FLTKLIB     =   $(shell $(FLTKCONFIG) --libs)
CFLAGS      +=  $(shell $(FLTKCONFIG) --cflags)
CPPFLAGS    +=  $(shell $(FLTKCONFIG) --cxxflags)
endif
POSTBUILD	=   $(FLTKCONFIG) --post

ifeq ($(FLTKLIB),)
ifneq ($(FLTKDIR),)
FLTKLIB     =   $(FLTKDIR)/lib/libfltk.a
CFLAGS      +=  -I$(FLTKDIR)
CPPFLAGS    +=  -I$(FLTKDIR)
endif
endif

VPATH		=	src:obj
LDFLAGS		+=	-L/usr/X11R6/lib
LIBFILES	=	-lstdc++ -lfltk_images -lfltk_jpeg -lfltk_png -lfltk_z -lfltk -lm -lc -lX11 -lpthread

# =============================
# Defines for MacOSX builds
# =============================
MACLDFLAGS	=	$(shell $(FLTKCONFIG) --ldflags) -arch i386 -arch ppc
MACLIBFILES	=	-lstdc++ `$(FLTKCONFIG) --ldstaticflags --use-images` --lm -lpthread

# =============================
# General make rules
# =============================
OBJECTS		=	$(SOURCES:.c=.o)
OBJECTSCPP	=	$(SOURCESCPP:.cpp=.o)
CLIENT_OBJS	=	$(CLIENT_SRC:.cpp=.o)
OBJDIR		=   obj


# =============================
# Define all source files below
# =============================
SOURCES		=	m100emu.c doins.c genwrap.c serial.c intelhex.c memory.c m100rom.c \
				m200rom.c n8201rom.c romstrings.c sound.c io.c m10rom.c kc85rom.c
SOURCESCPP	=	display.cpp setup.cpp periph.cpp disassemble.cpp file.cpp memedit.cpp cpuregs.cpp \
				a85parse.cpp assemble.cpp MString.cpp MStringArray.cpp rpn_eqn.cpp vtobj.cpp \
				Flu_DND.cpp flu_pixmaps.cpp Flu_Tree_Browser.cpp FluSimpleString.cpp ide.cpp \
				multiwin.cpp multiwin_icons.cpp	project.cpp multieditwin.cpp rememcfg.cpp \
				fl_usage_box.cpp remote.cpp socket.cpp serversocket.cpp lpt.cpp printer.cpp \
				fileprint.cpp hostprint.cpp fx80print.cpp chargen.cpp fl_action_icon.cpp fx80rom.cpp \
				vtpaper.cpp autofile.cpp clock.cpp fileview.cpp Flu_Wrap_Group.cpp \
				Flu_Button.cpp Flu_Combo_Box.cpp Flu_Combo_List.cpp Flu_Combo_Tree.cpp Flu_File_Chooser.cpp \
				flu_file_chooser_pixmaps.cpp Flu_Label.cpp Flu_Return_Button.cpp Flu_Separator.cpp \
				highlight.cpp idetabs.cpp linker.cpp My_Text_Display.cpp My_Text_Editor.cpp \
				socketdlg.cpp tpddclient.cpp pref_form.cpp

CLIENT_SRC	=	clientsocket.cpp vt_client_main.cpp socket.cpp

# ===============================
# Rule for building 2 exectuables
# ===============================
all:			virtualt vt_client

# ========================
# Rule to build VirtualT
# ========================
$(EXECUTABLE):	$(OBJECTS) $(OBJECTSCPP)

ifndef FLTKLIB
	@echo "FLTKDIR environment variable must be set first!"
	@echo "Or install the FLTK libraries"
	exit 1
else
	# Test if FLTK libraries built
	if ! test -f $(FLTKLIB); then \
		echo "Please ensure the FLTK, JPEG, PNG and ZLIB libraries and run make again"; \
		exit 1; \
	fi;
	@echo "Linking" $(EXECUTABLE)
	if test -f /Developer/Tools/Rez; then \
		cd obj; g++ $(MACLDFLAGS) $(OBJECTS) $(OBJECTSCPP) $(MACLIBFILES) -o ../$@ ; \
	else \
		cd obj; gcc $(LDFLAGS) $(OBJECTS) $(OBJECTSCPP) $(LIBFILES) -o ../$@ ; \
	fi;
	cd ..

	# If bulding on MacOS, post the resource file to the executable
	if test -f /Developer/Tools/Rez; then \
		$(POSTBUILD) $(EXECUTABLE); \
	fi;
endif

# ========================
# Rule to build vt_client
# ========================
$(CLIENT):		$(CLIENT_OBJS)
ifndef FLTKLIB
	@echo "FLTKDIR environment variable must be set first!"
	@echo "Or install the FLTK libraries"
	exit 1
else
	@echo "Linking" $(CLIENT)
#	cd obj; gcc $(LDFLAGS) $(OBJECTS) $(OBJECTSCPP) $(LIBFILES) -o ../$@
	if test -f /Developer/Tools/Rez; then \
		cd obj; g++ $(MACLDFLAGS) $(CLIENT_OBJS) $(MACLIBFILES) -o ../$@ ; \
	else \
		cd obj; gcc $(LDFLAGS) $(CLIENT_OBJS) $(LIBFILES) -o ../$@ ; \
	fi
	cd ..
endif


# ===============================
# Rule for compiling source files
# ===============================
.cpp.o:
ifndef FLTKLIB
	@echo "FLTKDIR environment variable must be set first!"
	@echo "Or install the FLTK libraries"
	exit 1
else
	@echo "Compiling" $<
	-mkdir -p obj; g++ $(CPPFLAGS) -c $< -o obj/$@
endif

.c.o:
ifndef FLTKLIB
	@echo "FLTKDIR environment variable must be set first!"
	@echo "Or install the FLTK libraries"
	exit 1
else
	@echo "Compiling" $<
	-mkdir -p obj; gcc $(CFLAGS) -c $< -o obj/$@
endif


# ==========================================
# Declare dependencies on header files below
# ==========================================
$(OBJECTS) $(OBJECTSCPP): m100emu.h GNUmakefile VirtualT.h
$(CLIENT_OBJS):	socket.h

disassemble.o:	disassemble.h io.h cpu.h periph.h memedit.h romstrings.h
display.o:		display.h io.h file.h setup.h periph.h memory.h memedit.h lpt.h clock.h
doins.o:		cpu.h io.h
file.o:			memory.h roms.h intelhex.h
io.o:			cpu.h gen_defs.h io.h serial.h display.h setup.h memory.h lpt.h clock.h
intelhex.o:		intelhex.h
m100emu.o:		io.h cpu.h doins.h display.h genwrap.h filewrap.h roms.h \
				intelhex.h setup.h memory.h do_instruct.h lpt.h clock.h
memedit.o:		memedit.h disassemble.h memory.h cpu.h
memory.o:		memory.h cpu.h io.h intelhex.h setup.h
periph.o:		periph.h serial.h setup.h display.h disassemble.h lpt.h
serial.o:		serial.h setup.h display.h
rememcfg.o:		rememcfg.h setup.h display.h
fl_usage_box.o: fl_usage_box.h
setup.o:		setup.h io.h serial.h memory.h memedit.h lpt.h clock.h
sound.o:		sound.h
m100rom.o m102rom.o m200rom.o n8201rom.o romstrings.o m10rom.o kc85rom.o: roms.h romstrings.h
remote.o:		remote.cpp m100emu.h socket.h serversocket.h socketexception.h
socket.o:		socket.h
serversocket.o:	serversocket.h
lpt.o:			lpt.h printer.h fileprint.h hostprint.h fx80print.h vtpaper.h
printer.o:		printer.h
fileprint.o:	printer.h fileprint.h vtobj.h MString.h
hostprint.o:	printer.h hostprint.h vtobj.h MString.h
fx80print.o:	printer.h fx80print.h vtpaper.h vtobj.h MString.h
chargen.o:		chargen.h
fl_action_icon.o:fl_action_icon.h
fx80rom.o:		
vtpaper.o:		printer.h vtpaper.h autofile.h
autofile.o:		autofile.h
clock.o:		clock.h
fileview.o:		display.h cpuregs.h disassemble.h fileview.h periph.h memedit.h file.h

# ==========
# asm files
# ==========
a85parse.o:		a85parse.h assemble.h
assemble.o:		assemble.h
MString.o:		MString.h
MStringArray.o:	MStringArray.h MString.h
rpn_eqn.o:		rpn_eqn.h
vtobj.o:		vtobj.h

# ==========
# ide files 
# ==========
Flu_DND.o:		FLU/Flu_DND.h
flu_pixmaps.o:	FLU/flu_pixmaps.h
Flu_Tree_Browser.o:	FLU/Flu_Tree_Browser.h
FluSimpleString.o:	FLU/FluSimpleString.h
ide.o:			ide.h vtobj.h multiwin.h multieditwin.h
multiwin.o:		multiwin.h vtobj.h
multiwin.o:		multieditwin.h multiwin.h vtobj.h
multiwin_icons.o:	multiwin_icons.h	
project.o:		project.h

# ==========
# client files 
# ==========
clientsocket.o:			clientsocket.h socket.h
vt_client_main.o:		clientsocket.h socket.h

# =============================
# Rule to clean all build files
# =============================
clean:
	@echo "=== cleaning ===";
	@echo "Objects..."
	cd obj; rm -f *.o; cd ..; 
	@echo "Executables..."
	rm -f virtualt 
	rm -f vt_client

# ================================================
# Provide info for building FLTK, Tiger, Leopard versions
# Windows, Linux, etc.
# ================================================
info:
	@echo
	@echo "Virtual T make Info"
	@echo "==================="
	@echo "  To build VirtualT, you must first build the FLTK libraries"
	@echo "  and the FLTK sub-components zlib, png and jpeg.  For MacOSX"
	@echo "  if you are tyring to build a universal build, you should"
	@echo "  configure fltk with"
	@echo
	@echo "        ./configure --with-archflags=\"-arch i386 -arch ppc\""
	@echo
	@echo "  When compiling FLTK for a version of OSX earlier than the"
	@echo "  running version, us a configure line something smilar to:"
	@echo
	@echo "        MACOSX_DEPLOYMENT_TARGET=10.4 ./configure CXX=\"/usr/bin/g++-4.0\" \\"
	@echo "           CC=\"/usr/bin/gcc-4.0\" \\"
	@echo "           CPP=\"/usr/bin/cpp-4.0\" \\"
	@echo "           CFLAGS=\"-isysroot /Developer/SDKs/MacOSX10.4u.sdk\" \\"
	@echo "           --with-archflags=\"-arch i386 -arch ppc\""
	@echo

