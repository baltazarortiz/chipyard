TARGET := riscv64-unknown-elf

libgloss_srcdir := ../software/libgloss
libgloss_specs := $(libgloss_srcdir)/util/htif_nano.specs
libgloss := libgloss/libgloss_htif.a

GCC=$(TARGET)-gcc
OBJDUMP=riscv64-unknown-elf-objdump
CFLAGS= -std=gnu99 -O2 -fno-common -fno-builtin-printf -Wall -specs=$(libgloss_specs)
LDFLAGS= -specs=$(libgloss_specs)

PROGRAMS = pwm blkdev accum charcount nic-loopback big-blkdev pingd

.PHONY: default
default: $(addsuffix .riscv,$(PROGRAMS))

.PHONY: dumps
dumps: $(addsuffix .dump,$(PROGRAMS))

%.o: %.S
	$(GCC) $(CFLAGS) -D__ASSEMBLY__=1 -c $< -o $@

%.o: %.c mmio.h
	$(GCC) $(CFLAGS) -c $< -o $@

%.riscv: %.o $(libgloss) $(libgloss_specs) htif.ld
	$(GCC) $(LDFLAGS) $< -o $@

%.dump: %.riscv
	$(OBJDUMP) -D $< > $@

# Build libgloss-htif locally

libgloss/Makefile: $(libgloss_srcdir)/configure
	mkdir -p $(dir $@)
	cd $(dir $@) && $(realpath $<) \
		--prefix=$(shell $(GCC) -print-sysroot) \
		--host=$(TARGET) \
		--disable-multilib

$(libgloss): libgloss/Makefile
	$(MAKE) -C $(dir $^)

.PHONY: libgloss
libgloss: $(libgloss)

.PHONY: clean
clean:
	rm -f *.riscv *.o *.dump
	rm -rf libgloss/
