NAME = OSSIA-APIToolkit
SUFFIX = dylib
API = ../../../i-score/API

CC_32 = /usr/bin/clang++ -arch i386
CC_64 = /usr/bin/clang++ -arch x86_64

#########################################

SRC32 = Sources/Debug/Printer.i386.o
SRC64 += Sources/Debug/Printer.x64.o
SRC32 += Sources/Setup/Network.i386.o
SRC64 += Sources/Setup/Network.x64.o
SRC32 += Sources/Setup/Project.i386.o
SRC64 += Sources/Setup/Project.x64.o
SRC32 += Sources/Setup/Score.i386.o
SRC64 += Sources/Setup/Score.x64.o

#########################################

INCLUDES = -I../../Headers/
INCLUDES += -IIncludes/
INCLUDES += -I$(API)/Headers
INCLUDES += -I3rdparty/rapidjson/include
INCLUDE_FILES := $(wildcard INCLUDES/*.h)

#########################################

LIBS = /usr/local/lib/OSSIA-API.dylib
LIBS += /System/Library/Frameworks/Carbon.framework/Versions/A/Carbon

#########################################

OPTIMIZATION_DEBUG = -O0 -DTT_ENABLE_ASSERTIONS
OPTIMIZATION_RELEASE = -O3

OPTIONS = -msse3 -gdwarf-2
OPTIONS += -std=c++11 
OPTIONS += -stdlib=libc++ # -U__STRICT_ANSI__ -D__STDC_FORMAT_MACROS
OPTIONS += -shared -mfpmath=sse
WARNINGS = -Wall -Wno-unknown-pragmas -Wno-trigraphs
DEFINES = -DTT_PLATFORM_MAC
#########################################

#DEFINES += -DOSSIA_API_TOOLKIT_EXPORTS

#########################################

CFLAGS = $(OPTIONS) $(DEFINES) $(INCLUDES) $(WARNINGS)
LDFLAGS =  -shared -mfpmath=sse $(OPTIONS) $(DEFINES) $(LIBS) $(WARNINGS)
LDFLAGS += -install_name "@rpath/$(NAME).dylib"

#########################################

Debug: OPTIMIZATION_FLAGS = $(OPTIMIZATION_DEBUG)
Debug: createdirs build

Release: OPTIMIZATION_FLAGS = $(OPTIMIZATION_RELEASE)
Release: createdirs build

createdirs:
	mkdir -p build

%.i386.o: %.cpp ${INCLUDE_FILES}
	$(CC_32) $(CFLAGS) $(OPTIMIZATION_FLAGS) -c $< -o $@
%.i386.mm.o: %.mm ${INCLUDE_FILES}
	$(CC_32) $(CFLAGS) $(OPTIMIZATION_FLAGS) -c $< -o $@
%.x64.o: %.cpp ${INCLUDE_FILES}
	$(CC_64) $(CFLAGS) $(OPTIMIZATION_FLAGS) -c $< -o $@
%.x64.mm.o: %.mm ${INCLUDE_FILES}
	$(CC_64) $(CFLAGS) $(OPTIMIZATION_FLAGS) -c $< -o $@

link: i386 x64 | $(SRC32) $(SRC64)

i386: $(SRC32)
	$(CC_32) $(LDFLAGS) $(OPTIMIZATION_FLAGS) -o build/$(NAME)-i386.dylib $(SRC32)
	libtool -static -o build/lib$(NAME)-i386.a $(SRC32)

x64: $(SRC64)
	$(CC_64) $(LDFLAGS) $(OPTIMIZATION_FLAGS) -o build/$(NAME)-x86_64.dylib $(SRC64)
	libtool -static -o build/lib$(NAME)-x86_64.a $(SRC64)

lipo: | link
	lipo build/$(NAME)-i386.dylib build/$(NAME)-x86_64.dylib -create -output build/$(NAME).dylib
	lipo build/lib$(NAME)-i386.a build/lib$(NAME)-x86_64.a -create -output build/lib$(NAME).a
    
	cp build/$(NAME).dylib /usr/local/lib # useful to run Documentation/Examples projects

clean:
	rm -f $(SRC32) $(SRC64)
	rm -rf build

build: | lipo

