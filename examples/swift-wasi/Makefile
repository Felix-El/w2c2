CFLAGS += -I../../w2c2 -O0 -g
LDFLAGS += -lm -L../../wasi -lw2c2wasi
W2C2 ?= ../../w2c2/w2c2

MODE ?= gnu-ld

ifeq ($(shell uname -s),Darwin)
    ifeq ($(MODE),gnu-ld)
        # at least Darwin 11 / Mac OS X 10.7 ?
        ifeq ($(shell test `uname -r | egrep -o "^[0-9]+"` -gt 10; echo $$?),0)
            MODE = sectcreate1
        else
            MODE = sectcreate2
        endif
        LDFLAGS += -sectcreate __DATA __datasegments datasegments
    endif
endif
ifeq ($(MODE),gnu-ld)
    OBJS += datasegments.o
endif

swift-wasi: swiftwasi.o
	$(MAKE) -f Submake CC="$(CC)" CFLAGS="$(CFLAGS)" LDFLAGS="$(LDFLAGS)" OBJS="$(OBJS)"

swiftwasi.c: swift-wasi.wasm
	$(W2C2) -d $(MODE) -f 100 $^ $@

.build/wasm32-unknown-wasi/release/swift-wasi.wasm:
	swift build -Xswiftc -Osize -c release --triple wasm32-unknown-wasi

swift-wasi.wasm: .build/wasm32-unknown-wasi/release/swift-wasi.wasm
	wasm-opt --strip-dwarf -O4 -o $@ $<

.PHONY: clean
clean:
	rm -f swift-wasi swiftwasi.h datasegments $(filter-out main.c,$(wildcard *.c)) *.o
