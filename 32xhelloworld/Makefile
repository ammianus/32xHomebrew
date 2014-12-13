GENDEV=C:/bin/_gcc/gen
ROOTDIR = $(GENDEV)
#LDSCRIPTSDIR = $(ROOTDIR)/ldscripts

SHPREFIX = $(ROOTDIR)/sh-elf/bin/sh-elf-
MDPREFIX = $(ROOTDIR)/m68k-elf/bin/m68k-elf-
MDLD = $(MDPREFIX)ld
MDAS = $(MDPREFIX)as
SHLD = $(SHPREFIX)ld
SHCC = $(SHPREFIX)gcc
SHAS = $(SHPREFIX)as
SHOBJC = $(SHPREFIX)objcopy
RM = rm -f

CCFLAGS = -m2 -mb -O3 -Wall -Wformat -c -fomit-frame-pointer
HWCCFLAGS = -m2 -mb -O1 -Wall -c -fomit-frame-pointer
LINKFLAGS = -T mars-helloworld.ld -Wl,-Map=output.map -nostdlib --warn-section-align --sort-common

INCS = -I. -I$(GENDEV)/sh-elf/include -I$(GENDEV)/sh-elf/sh-elf/include

LIBS = -L$(GENDEV)/sh-elf/sh-elf/lib -L$(GENDEV)/sh-elf/lib/gcc/sh-elf/4.5.2 -lc -lgcc -lgcc-Os-4-200 -lnosys -lm

OBJS = sh2_crt0.o main.o slave.o hw_32x.o font.o lzss_decode.o 32x_images.o graphics.o shared_objects.o 

#checks for changes to *.bmp files in images directory
#http://stackoverflow.com/q/2452634/471658
BMPFLAG=$(shell ls -l images/*.bmp | md5sum | sed 's/[[:space:]].*//').files

all: m68k_crt0.bin m68k_crt1.bin helloworld.32x

helloworld.32x: helloworld.elf
	$(SHOBJC) -O binary $< temp.bin
	dd if=temp.bin of=$@ bs=64K conv=sync

helloworld.elf: $(OBJS)
	$(SHCC) $(LINKFLAGS) $(OBJS) $(LIBS) -o helloworld.elf

m68k_crt0.bin: m68k_crt0.s
	$(MDAS) -m68000 --register-prefix-optional -o m68k_crt0.o m68k_crt0.s
	$(MDLD) -T $(GENDEV)/ldscripts/md.ld --oformat binary -o m68k_crt0.bin m68k_crt0.o

m68k_crt1.bin: m68k_crt1.s
	$(MDAS) -m68000 --register-prefix-optional -o m68k_crt1.o m68k_crt1.s
	$(MDLD) -T $(GENDEV)/ldscripts/md.ld --oformat binary -o m68k_crt1.bin m68k_crt1.o

hw_32x.o: hw_32x.c
	$(SHCC) $(HWCCFLAGS) $< -o $@

main.o: main.c
	$(SHCC) $(CCFLAGS) $< -o $@

slave.o: slave.c
	$(SHCC) $(CCFLAGS) $< -o $@
	
%.o: %.c
	$(SHCC) $(CCFLAGS) $< -o $@

%.o: %.s
	$(SHAS) --small -o $@ $<

		
clean:
	$(RM) *.o *.bin *.elf *.map *prev.bmp *.32x image-encodeX.cmd image_lzss.s *.files image_data.h *.pal *.lzs
