# ##########################################################################
# LZ4 programs - Makefile
# Copyright (C) Yann Collet 2011-2016
#
# GPL v2 License
#
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 2 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License along
# with this program; if not, write to the Free Software Foundation, Inc.,
# 51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.
#
# You can contact the author at :
#  - LZ4 homepage : http://www.lz4.org
#  - LZ4 source repository : https://github.com/Cyan4973/lz4
# ##########################################################################
# lz4 : Command Line Utility, supporting gzip-like arguments
# lz4c  : CLU, supporting also legacy lz4demo arguments
# lz4c32: Same as lz4c, but forced to compile in 32-bits mode
# fuzzer  : Test tool, to check lz4 integrity on target platform
# fuzzer32: Same as fuzzer, but forced to compile in 32-bits mode
# frametest  : Test tool, to check lz4frame integrity on target platform
# frametest32: Same as frametest, but forced to compile in 32-bits mode
# fullbench  : Precisely measure speed for each LZ4 function variant
# fullbench32: Same as fullbench, but forced to compile in 32-bits mode
# datagen : generates synthetic data samples for tests & benchmarks
# ##########################################################################

DESTDIR ?=
PREFIX  ?= /usr/local
BINDIR  := $(PREFIX)/bin
MANDIR  := $(PREFIX)/share/man/man1
LZ4DIR  := ../lib

CFLAGS  ?= -O3   # can select custom flags. For example : CFLAGS="-O2 -g" make
CFLAGS  += -Wall -Wextra -Wundef -Wcast-qual -Wcast-align -Wshadow -Wswitch-enum -Wdeclaration-after-statement -Wstrict-prototypes
CFLAGS  += $(MOREFLAGS)
CPPFLAGS:= -I$(LZ4DIR) -DXXH_NAMESPACE=LZ4_ -DLZ4_DLL_EXPORT=1
FLAGS   := $(CFLAGS) $(CPPFLAGS) $(LDFLAGS)


# Define *.exe as extension for Windows systems
ifneq (,$(filter Windows%,$(OS)))
EXT =.exe
VOID = nul
else
EXT =
VOID = /dev/null
endif


# Default test parameters
TEST_FILES   := COPYING
FUZZER_TIME  := -T9mn


default: lz4

m32: lz4c32 fullbench32 fuzzer32 frametest32

bins: lz4 lz4c fullbench fuzzer frametest datagen

all: bins m32

lz4: $(LZ4DIR)/lz4.o $(LZ4DIR)/lz4hc.o $(LZ4DIR)/lz4frame.o $(LZ4DIR)/xxhash.o bench.o lz4io.o lz4cli.o
	$(CC) $(FLAGS) $^ -o $@$(EXT)

lz4c  : $(LZ4DIR)/lz4.o $(LZ4DIR)/lz4hc.o $(LZ4DIR)/lz4frame.o $(LZ4DIR)/xxhash.o bench.o lz4io.o lz4cli.o
	$(CC) $(FLAGS) -DENABLE_LZ4C_LEGACY_OPTIONS $^ -o $@$(EXT)

lz4c32: $(LZ4DIR)/lz4.c $(LZ4DIR)/lz4hc.c $(LZ4DIR)/lz4frame.c $(LZ4DIR)/xxhash.c bench.c lz4io.c lz4cli.c
	$(CC) -m32 $(FLAGS) -DENABLE_LZ4C_LEGACY_OPTIONS $^ -o $@$(EXT)

fullbench  : $(LZ4DIR)/lz4.o $(LZ4DIR)/lz4hc.o $(LZ4DIR)/lz4frame.o $(LZ4DIR)/xxhash.o fullbench.c
	$(CC) $(FLAGS) $^ -o $@$(EXT)

fullbench32: $(LZ4DIR)/lz4.c $(LZ4DIR)/lz4hc.c $(LZ4DIR)/lz4frame.c $(LZ4DIR)/xxhash.c fullbench.c
	$(CC) -m32 $(FLAGS) $^ -o $@$(EXT)

fuzzer  : $(LZ4DIR)/lz4.o $(LZ4DIR)/lz4hc.o $(LZ4DIR)/xxhash.o fuzzer.c
	$(CC) $(FLAGS) $^ -o $@$(EXT)

fuzzer32: $(LZ4DIR)/lz4.c $(LZ4DIR)/lz4hc.c $(LZ4DIR)/xxhash.c fuzzer.c
	$(CC) -m32 $(FLAGS) $^ -o $@$(EXT)

frametest: $(LZ4DIR)/lz4frame.o $(LZ4DIR)/lz4.o $(LZ4DIR)/lz4hc.o $(LZ4DIR)/xxhash.o frametest.c
	$(CC) $(FLAGS) $^ -o $@$(EXT)

frametest32: $(LZ4DIR)/lz4frame.c $(LZ4DIR)/lz4.c $(LZ4DIR)/lz4hc.c $(LZ4DIR)/xxhash.c frametest.c
	$(CC) -m32 $(FLAGS) $^ -o $@$(EXT)

datagen : datagen.o datagencli.c
	$(CC) $(FLAGS) $^ -o $@$(EXT)

clean:
	@$(MAKE) -C $(LZ4DIR) $@ > $(VOID)
	@$(RM) core *.o *.test tmp* \
        lz4$(EXT) lz4c$(EXT) lz4c32$(EXT) \
        fullbench$(EXT) fullbench32$(EXT) \
        fuzzer$(EXT) fuzzer32$(EXT) \
        frametest$(EXT) frametest32$(EXT) \
        datagen$(EXT)
	@echo Cleaning completed


#------------------------------------------------------------------------
#make install is validated only for Linux, OSX, kFreeBSD, Hurd and
#FreeBSD targets
ifneq (,$(filter $(shell uname),Linux Darwin GNU/kFreeBSD GNU FreeBSD))

install: lz4$(EXT) lz4c$(EXT)
	@echo Installing binaries
	@install -d -m 755 $(DESTDIR)$(BINDIR)/ $(DESTDIR)$(MANDIR)/
	@install -m 755 lz4$(EXT) $(DESTDIR)$(BINDIR)/lz4$(EXT)
	@ln -sf lz4$(EXT) $(DESTDIR)$(BINDIR)/lz4cat$(EXT)
	@ln -sf lz4$(EXT) $(DESTDIR)$(BINDIR)/unlz4$(EXT)
	@install -m 755 lz4c$(EXT) $(DESTDIR)$(BINDIR)/lz4c$(EXT)
	@echo Installing man pages
	@install -m 644 lz4.1 $(DESTDIR)$(MANDIR)/lz4.1
	@ln -sf lz4.1 $(DESTDIR)$(MANDIR)/lz4c.1
	@ln -sf lz4.1 $(DESTDIR)$(MANDIR)/lz4cat.1
	@ln -sf lz4.1 $(DESTDIR)$(MANDIR)/unlz4.1
	@echo lz4 installation completed

uninstall:
	@$(RM) $(DESTDIR)$(BINDIR)/lz4cat$(EXT)
	@$(RM) $(DESTDIR)$(BINDIR)/unlz4$(EXT)
	@$(RM) $(DESTDIR)$(BINDIR)/lz4$(EXT)
	@$(RM) $(DESTDIR)$(BINDIR)/lz4c$(EXT)
	@$(RM) $(DESTDIR)$(MANDIR)/lz4.1
	@$(RM) $(DESTDIR)$(MANDIR)/lz4c.1
	@$(RM) $(DESTDIR)$(MANDIR)/lz4cat.1
	@$(RM) $(DESTDIR)$(MANDIR)/unlz4.1
	@echo lz4 programs successfully uninstalled

test: test-lz4 test-lz4c test-frametest test-fullbench test-fuzzer test-mem

test32: test-lz4c32 test-frametest32 test-fullbench32 test-fuzzer32 test-mem32

test-all: test test32

test-lz4-sparse: lz4 datagen
	@echo "\n ---- test sparse file support ----"
	./datagen -g5M  -P100 > tmpSrc
	./lz4 -B4D tmpSrc | ./lz4 -dv --sparse > tmpB4
	diff -s tmpSrc tmpB4
	./lz4 -B5D tmpSrc | ./lz4 -dv --sparse > tmpB5
	diff -s tmpSrc tmpB5
	./lz4 -B6D tmpSrc | ./lz4 -dv --sparse > tmpB6
	diff -s tmpSrc tmpB6
	./lz4 -B7D tmpSrc | ./lz4 -dv --sparse > tmpB7
	diff -s tmpSrc tmpB7
	./lz4 tmpSrc | ./lz4 -dv --no-sparse > tmpNoSparse
	diff -s tmpSrc tmpNoSparse
	ls -ls tmp*
	./datagen -s1 -g1200007 -P100 | ./lz4 | ./lz4 -dv --sparse > tmpOdd   # Odd size file (to generate non-full last block)
	./datagen -s1 -g1200007 -P100 | diff -s - tmpOdd
	ls -ls tmpOdd
	@$(RM) tmp*
	@echo "\n Compatibility with Console :"
	echo "Hello World 1 !" | ./lz4 | ./lz4 -d -c
	echo "Hello World 2 !" | ./lz4 | ./lz4 -d | cat
	echo "Hello World 3 !" | ./lz4 --no-frame-crc | ./lz4 -d -c
	@echo "\n Compatibility with Append :"
	./datagen -P100 -g1M > tmp1M
	cat tmp1M tmp1M > tmp2M
	./lz4 -B5 -v tmp1M tmpC
	./lz4 -d -v tmpC tmpR
	./lz4 -d -v tmpC >> tmpR
	ls -ls tmp*
	diff tmp2M tmpR
	@$(RM) tmp*

test-lz4-contentSize: lz4 datagen
	@echo "\n ---- test original size support ----"
	./datagen -g15M > tmp
	./lz4 -v tmp | ./lz4 -t
	./lz4 -v --content-size tmp | ./lz4 -d > tmp2
	diff -s tmp tmp2
	# test large size [2-4] GB
	@./datagen -g3G -P100 | ./lz4 | ./lz4 --decompress --force --sparse - tmp
	@ls -ls tmp
	./lz4 --quiet --content-size tmp | ./lz4 --verbose --decompress --force --sparse - tmp2
	@ls -ls tmp2
	@$(RM) tmp*

test-lz4-frame-concatenation: lz4 datagen
	@echo "\n ---- test frame concatenation ----"
	@echo -n > empty.test
	@echo hi > nonempty.test
	cat nonempty.test empty.test nonempty.test > orig.test
	@./lz4 -zq empty.test > empty.lz4.test
	@./lz4 -zq nonempty.test > nonempty.lz4.test
	cat nonempty.lz4.test empty.lz4.test nonempty.lz4.test > concat.lz4.test
	./lz4 -d concat.lz4.test > result.test
	sdiff orig.test result.test
	@$(RM) *.test
	@echo frame concatenation test completed

test-lz4-multiple: lz4 datagen
	@echo "\n ---- test multiple files ----"
	@./datagen -s1        > tmp1 2> $(VOID)
	@./datagen -s2 -g100K > tmp2 2> $(VOID)
	@./datagen -s3 -g1M   > tmp3 2> $(VOID)
	./lz4 -f -m tmp*
	ls -ls tmp*
	@$(RM) tmp1 tmp2 tmp3
	./lz4 -df -m *.lz4
	ls -ls tmp*
	./lz4 -f -m tmp1 notHere tmp2; echo $$?
	@$(RM) tmp*

test-lz4-basic: lz4 datagen
	@echo "\n ---- test lz4 basic compression/decompression ----"
	./datagen -g0     | ./lz4 -v     | ./lz4 -t
	./datagen -g16KB  | ./lz4 -9     | ./lz4 -t
	./datagen -g20KB > tmpSrc
	./lz4 < tmpSrc | ./lz4 -d > tmpRes
	diff -q tmpSrc tmpRes
	./lz4 --no-frame-crc < tmpSrc | ./lz4 -d > tmpRes
	diff -q tmpSrc tmpRes
	./datagen         | ./lz4        | ./lz4 -t
	./datagen -g6M -P99 | ./lz4 -9BD | ./lz4 -t
	./datagen -g17M   | ./lz4 -9v    | ./lz4 -qt
	./datagen -g33M   | ./lz4 --no-frame-crc | ./lz4 -t
	./datagen -g256MB | ./lz4 -vqB4D | ./lz4 -t
	./datagen -g6GB   | ./lz4 -vqB5D | ./lz4 -qt
	./datagen -g6GB   | ./lz4 -vq9BD | ./lz4 -qt
	@$(RM) tmp*

test-lz4: lz4 datagen test-lz4-basic test-lz4-multiple test-lz4-sparse test-lz4-contentSize test-lz4-frame-concatenation
	@echo "\n ---- test pass-through ----"
	./datagen | ./lz4 -tf

test-lz4c: lz4c datagen
	@echo "\n ---- test lz4c version ----"
	./datagen -g256MB | ./lz4c -l -v    | ./lz4c   -t

test-interop-32-64: lz4 lz4c32 datagen
	@echo "\n ---- test interoperability 32-bits -vs- 64 bits ----"
	./datagen -g16KB  | ./lz4c32 -9     | ./lz4    -t
	./datagen -P10    | ./lz4    -9B4   | ./lz4c32 -t
	./datagen         | ./lz4c32        | ./lz4    -t
	./datagen -g1M    | ./lz4    -3B5   | ./lz4c32 -t
	./datagen -g256MB | ./lz4c32 -vqB4D | ./lz4    -qt
	./datagen -g1G -P90 | ./lz4         | ./lz4c32 -t
	./datagen -g6GB   | ./lz4c32 -vq9BD | ./lz4    -qt

test-lz4c32-basic: lz4c32 datagen
	@echo "\n ---- test lz4c32 32-bits version ----"
	./datagen -g16KB  | ./lz4c32 -9     | ./lz4c32 -t
	./datagen         | ./lz4c32        | ./lz4c32 -t
	./datagen -g256MB | ./lz4c32 -vqB4D | ./lz4c32 -qt
	./datagen -g6GB   | ./lz4c32 -vqB5D | ./lz4c32 -qt

test-lz4c32: test-lz4c32-basic test-interop-32-64

test-fullbench: fullbench
	./fullbench --no-prompt $(NB_LOOPS) $(TEST_FILES)

test-fullbench32: fullbench32
	./fullbench32 --no-prompt $(NB_LOOPS) $(TEST_FILES)

test-fuzzer: fuzzer
	./fuzzer $(FUZZER_TIME)

test-fuzzer32: fuzzer32
	./fuzzer32 $(FUZZER_TIME)

test-frametest: frametest
	./frametest $(FUZZER_TIME)

test-frametest32: frametest32
	./frametest32 $(FUZZER_TIME)

test-mem: lz4 datagen fuzzer frametest fullbench
	@echo "\n ---- valgrind tests : memory analyzer ----"
	valgrind --leak-check=yes --error-exitcode=1 ./datagen -g50M > $(VOID)
	./datagen -g16KB > tmp
	valgrind --leak-check=yes --error-exitcode=1 ./lz4 -9 -BD -f tmp $(VOID)
	./datagen -g16KB -s2 > tmp2
	./datagen -g16KB -s3 > tmp3
	valgrind --leak-check=yes --error-exitcode=1 ./lz4 --force --multiple tmp tmp2 tmp3
	./datagen -g16MB > tmp
	valgrind --leak-check=yes --error-exitcode=1 ./lz4 -9 -B5D -f tmp tmp2
	valgrind --leak-check=yes --error-exitcode=1 ./lz4 -t tmp2
	valgrind --leak-check=yes --error-exitcode=1 ./lz4 -bi1 tmp
	valgrind --leak-check=yes --error-exitcode=1 ./fullbench -i1 tmp tmp2
	./datagen -g256MB > tmp
	valgrind --leak-check=yes --error-exitcode=1 ./lz4 -B4D -f -vq tmp $(VOID)
	$(RM) tmp*
	valgrind --leak-check=yes --error-exitcode=1 ./fuzzer -i64 -t1
	valgrind --leak-check=yes --error-exitcode=1 ./frametest -i256

test-mem32: lz4c32 datagen
# unfortunately, valgrind doesn't seem to work with non-native binary...

endif
