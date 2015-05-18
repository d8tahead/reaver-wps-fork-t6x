CC=mips-openwrt-linux-uclibc-gcc
LIBWPS_DIR=libwps
INC=-I$(LIBWPS_DIR)
prefix=/usr
exec_prefix=/usr
CONFDIR=/etc/reaver
CFLAGS=-DCONF_DIR='"$(CONFDIR)"' -DREAVER_DATABASE='"$(CONFDIR)/reaver.db"' -Wall -Os -pipe -mips32r2 -mtune=mips32r2 -fno-caller-saves -fhonour-copts -Wno-error=unused-but-set-variable -msoft-float -I/home/mk5/MK5/staging_dir/target-mips_r2_uClibc-0.9.33.2/usr/include -I/home/mk5/MK5/staging_dir/target-mips_r2_uClibc-0.9.33.2/include -I/home/mk5/MK5/staging_dir/toolchain-mips_r2_gcc-4.6-linaro_uClibc-0.9.33.2/usr/include -I/home/mk5/MK5/staging_dir/toolchain-mips_r2_gcc-4.6-linaro_uClibc-0.9.33.2/include 
LDFLAGS=$(LIBWPS_DIR)/*.o wps/*.o tls/bignum.o tls/libtls.a utils/libutils.a crypto/libcrypto.a lwe/libiw.a -ldl -lm -lpcap -lsqlite3 -L/home/mk5/MK5/staging_dir/target-mips_r2_uClibc-0.9.33.2/usr/lib -L/home/mk5/MK5/staging_dir/target-mips_r2_uClibc-0.9.33.2/lib -L/home/mk5/MK5/staging_dir/toolchain-mips_r2_gcc-4.6-linaro_uClibc-0.9.33.2/usr/lib -L/home/mk5/MK5/staging_dir/toolchain-mips_r2_gcc-4.6-linaro_uClibc-0.9.33.2/lib 

all: wash reaver

wash: wps libiw libwps.o argsparser.o globule.o init.o misc.o 80211.o iface.o
	$(CC) $(CFLAGS) $(INC) wpsmon.c *.o $(LDFLAGS) -o wash

reaver: wps libiw libwps.o argsparser.o globule.o init.o sigint.o sigalrm.o misc.o cracker.o
	$(CC) $(CFLAGS) $(INC) wpscrack.c *.o $(LDFLAGS) -o reaver

libwps.o:
	(cd $(LIBWPS_DIR) && make)

wps: libutils libcrypto
	(cd wps && make)

libutils:
	(cd utils && make)

libcrypto: libtls
	(cd crypto && make)

libtls:
	(cd tls && make)

libiw:
	(cd lwe && make BUILD_STATIC=y libiw.a)

init.o:
	$(CC) $(CFLAGS) init.c -c

sql.o:
	$(CC) $(CFLAGS) $(INC) sql.c -c

crc.o:
	$(CC) $(CFLAGS) crc.c -c

keys.o:
	$(CC) $(CFLAGS) keys.c -c

argsparser.o: globule.o
	$(CC) $(CFLAGS) $(INC) argsparser.c -c

sigint.o: globule.o
	$(CC) $(CFLAGS) $(INC) sigint.c -c

exchange.o: globule.o send.o sigalrm.o 80211.o
	$(CC) $(CFLAGS) $(INC) exchange.c -c

send.o: globule.o builder.o sigalrm.o
	$(CC) $(CFLAGS) send.c -c

session.o: globule.o
	$(CC) $(CFLAGS) $(INC) session.c -c

80211.o: globule.o sql.o builder.o crc.o
	$(CC) $(CFLAGS) $(INC) 80211.c -c

iface.o: globule.o
	$(CC) $(CFLAGS) iface.c -c

sigalrm.o: globule.o
	$(CC) $(CFLAGS) sigalrm.c -c

misc.o: globule.o 
	$(CC) $(CFLAGS) misc.c -c

builder.o: globule.o
	$(CC) $(CFLAGS) builder.c -c

pins.o: globule.o keys.o
	$(CC) $(CFLAGS) pins.c -c

cracker.o: globule.o init.o pins.o iface.o exchange.o session.o 80211.o
	$(CC) $(CFLAGS) $(INC) cracker.c -c

globule.o:
	$(CC) $(CFLAGS) globule.c -c 

install: cleanbin
	if [ ! -d $(CONFDIR) ]; then mkdir -p $(CONFDIR); fi
	if [ ! -e $(CONFDIR)/reaver.db ]; then cp reaver.db $(CONFDIR)/reaver.db && chmod -R a+rw $(CONFDIR); fi
	if [ -e wash ]; then cp wash /usr/bin/wash; fi
	if [ -e reaver ]; then cp reaver /usr/bin/reaver; fi

clean:
	rm -f *~ *.o reaver wash
	rm -rf *.cache config.status config.log
	(cd utils && make clean)
	(cd tls && make clean)
	(cd crypto && make clean)
	(cd wps && make clean)
	(cd lwe && make realclean)
	(cd $(LIBWPS_DIR) && make clean)
	rm -f Makefile config.h

cleanbin: 
	rm -f /usr/bin/reaver /usr/bin/wash /usr/bin/walsh

distclean: cleanbin clean
	rm -rf $(CONFDIR)
