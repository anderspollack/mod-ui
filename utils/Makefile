
CC  ?= gcc
CXX ?= g++

CFLAGS += -Wall -Wextra -std=c99 -fPIC -O3
CXXFLAGS += -Wall -Wextra -Wshadow -std=gnu++0x -fPIC -O3
LDFLAGS += -Wl,-O1 -Wl,--no-undefined -Wl,--strip-all

ifeq ($(shell pkg-config --atleast-version=0.22.0 lilv-0 && echo true), true)
CXXFLAGS += -DHAVE_NEW_LILV
endif

all: build

build: libmod_utils.so

clean:
	rm -f *.so *.o sha1/*.o

libmod_utils.so: utils_jack.cpp.o utils_lilv.cpp.o sha1/sha1.c.o
	$(CXX) $^ $(LDFLAGS) -shared $(shell pkg-config --libs alsa jack lilv-0) -o $@

sha1/sha1.c.o: sha1/sha1.c
	$(CC) $< $(CFLAGS) -c -o $@

utils_jack.cpp.o: utils_jack.cpp utils.h
	$(CXX) $< $(CXXFLAGS) $(shell pkg-config --cflags alsa jack) -c -o $@

utils_lilv.cpp.o: utils_lilv.cpp sha1/sha1.h utils.h
	$(CXX) $< $(CXXFLAGS) $(shell pkg-config --cflags lilv-0) -c -o $@

test.cpp.o: test.cpp utils.h
	$(CXX) $< $(CXXFLAGS) -c -o $@

test: utils_jack.cpp.o utils_lilv.cpp.o test.cpp.o sha1/sha1.c.o
	$(CXX) $^ $(LDFLAGS) $(shell pkg-config --libs alsa jack lilv-0) -o $@

test-run: test
	valgrind --leak-check=full --show-leak-kinds=all --partial-loads-ok=yes ./test
