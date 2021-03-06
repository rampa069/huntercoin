CXX=g++

DEFS=-D_MT -DNOPCH -DFOURWAYSSE2 -DUSE_SSL 

# Detect MinGW
MINGW=$(shell uname -s|grep -i mingw32)

# Link boost statically
DEFS += -DBOOST_THREAD_USE_LIB

INCLUDEPATHS= \
	-I../libs/openssl-1.0.1e/include \
	-I../libs/db-4.7.25.NC/build_unix \
	-I../libs/boost_1_50_0

LIBPATHS= \
	-L../libs/openssl-1.0.1e \
	-L../libs/db-4.7.25.NC/build_unix \
	-L../libs/boost_1_50_0/stage/lib

LIBBOOST_SUFFIX=

LIBS= \
 -Wl,-Bstatic \
   -l boost_system$(LIBBOOST_SUFFIX) \
   -l boost_filesystem$(LIBBOOST_SUFFIX) \
   -l boost_program_options$(LIBBOOST_SUFFIX) \
   -l boost_thread$(LIBBOOST_SUFFIX) \
   -l db_cxx \
   -l ssl \
   -l crypto

huntercoind: USE_UPNP:=0
	ifdef USE_UPNP
		LIBS += -l miniupnpc
		DEFS += -DUSE_UPNP=$(USE_UPNP)
		INCLUDEPATHS+=-I../libs/miniupnpc-1.8.20130211
		LIBPATHS+=-I../libs/miniupnpc-1.8.20130211
	endif
	ifneq "$(MINGW)" ""
		# MinGW-specific
		DEFS += -D__NO_SYSTEM_INCLUDES -D__WXMSW__
		LIBS += -l mingwthrd -lws2_32 -lshlwapi -lmswsock -lole32 -loleaut32 -luuid -lgdi32
		LIBBOOST_SUFFIX=-mgw46-mt-1_50
	else
		# Linux-specific
		LIBS+= \
			-Wl,-Bdynamic \
			-l z \
			-l dl \
			-l pthread
	endif

CXXFLAGS=-O2 -Wno-invalid-offsetof -Wformat $(DEFS) $(INCLUDEPATHS)

HEADERS=headers.h strlcpy.h serialize.h uint256.h util.h key.h bignum.h base58.h scrypt.h \
    script.h allocators.h db.h walletdb.h crypter.h net.h irc.h keystore.h main.h wallet.h bitcoinrpc.h uibase.h ui.h noui.h init.h auxpow.h

OBJS= \
    obj/auxpow.o \
    obj/scrypt.o \
    obj/util.o \
    obj/key.o \
    obj/script.o \
    obj/db.o \
    obj/walletdb.o \
    obj/crypter.o \
    obj/net.o \
    obj/irc.o \
    obj/keystore.o \
    obj/main.o \
    obj/wallet.o \
    obj/bitcoinrpc.o \
    obj/init.o \
    obj/huntercoin.o \
    obj/gamestate.o \
    obj/gamemap.o \
    obj/gamedb.o \
    obj/gametx.o \
    cryptopp/obj/sha.o \
    cryptopp/obj/cpu.o

ifdef USE_SSE2
DEFS += -DUSE_SSE2
OBJS_SSE2= obj/scrypt-sse2.o
OBJS += $(OBJS_SSE2)
endif

all: huntercoind

obj/%.o: %.cpp $(HEADERS)
	$(CXX) -c $(CXXFLAGS) -DGUI -o $@ $<

cryptopp/obj/%.o: cryptopp/%.cpp
	$(CXX) -c $(CXXFLAGS) -O2 -o $@ $<

obj/%.o: %.cpp $(HEADERS)
	$(CXX) -c $(CXXFLAGS) -o $@ $<

obj/huntercoin.o: huntercoin.h gamestate.h gamedb.h

obj/gamestate.o: gamestate.h gamemap.h

obj/gamemap.o: gamemap.h

obj/gamedb.o: gamestate.h gamedb.h gametx.h

obj/gametx.o: gametx.h gamestate.h

huntercoind: $(OBJS)
	$(CXX) $(CXXFLAGS) -o $@ $(LIBPATHS) $^ $(LIBS)

clean:
	-rm -f huntercoin huntercoind
	-rm -f obj/*.o
	-rm -f cryptopp/obj/*.o
	-rm -f headers.h.gch
