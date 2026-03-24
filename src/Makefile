# include file sources.mk mà bạn vừa làm
include sources.mk

# Tên file thực thi đầu ra
TARGET = c1m2

# Cờ chung cho cả 2 nền tảng
GCFLAGS = -Wall -Werror -g -O0 -std=c99

# --- PHÂN NHÁNH CẤU HÌNH THEO NỀN TẢNG ---
ifeq ($(PLATFORM),MSP432)
    # Trình biên dịch cho ARM
    CC = arm-none-eabi-gcc
    LD = arm-none-eabi-ld
    SIZE = arm-none-eabi-size
    OBJDUMP = arm-none-eabi-objdump

    # Cờ kiến trúc lõi ARM Cortex-M4
    CPU = cortex-m4
    ARCH = thumb
    SPECS = nosys.specs

    # Đường dẫn file linker
    LINKER_FILE = ../msp432p401r.lds

    # Tổng hợp các cờ
    LDFLAGS = -Wl,-Map=$(TARGET).map -T $(LINKER_FILE)
    CFLAGS = $(GCFLAGS) -mcpu=$(CPU) -m$(ARCH) --specs=$(SPECS) -march=armv7e-m -mfloat-abi=hard -mfpu=fpv4-sp-d16
    CPPFLAGS = -DMSP432 $(INCLUDES)

else
    # Trình biên dịch cho máy tính (HOST) mặc định
    CC = gcc
    LD = ld
    SIZE = size
    OBJDUMP = objdump

    # Tổng hợp các cờ
    LDFLAGS = -Wl,-Map=$(TARGET).map
    CFLAGS = $(GCFLAGS)
    CPPFLAGS = -DHOST $(INCLUDES)
endif

# --- CÁC QUY TẮC BIÊN DỊCH (TARGETS) ---

# Tạo file object (.o) từ file C
%.o : %.c
	$(CC) -c $< $(CFLAGS) $(CPPFLAGS) -o $@

# Tạo file tiền xử lý (.i)
%.i : %.c
	$(CC) -E $< $(CPPFLAGS) -o $@

# Tạo file hợp ngữ assembly (.asm)
%.asm : %.c
	$(CC) -S $< $(CFLAGS) $(CPPFLAGS) -o $@

# Tạo file dependency (.d)
%.d : %.c
	$(CC) -M $< $(CPPFLAGS) -o $@

# Biên dịch tất cả thành file .o nhưng không link
.PHONY: compile-all
compile-all: $(SOURCES:.c=.o)

# Target build cuối cùng: Link các file .o thành c1m2.out và in ra dung lượng (size)
.PHONY: build
build: $(TARGET).out

$(TARGET).out: $(SOURCES:.c=.o)
	$(CC) $(SOURCES:.c=.o) $(CFLAGS) $(CPPFLAGS) $(LDFLAGS) -o $@
	$(SIZE) $@

# Dọn dẹp rác
.PHONY: clean
clean:
	rm -f *.o *.i *.asm *.d *.map *.out