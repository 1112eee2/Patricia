.PHONY: default build datagen aarch64 cross-aarch64 cross-aarch64-clang avx2 avx2_bmi2 clean

EXE := patricia
SOURCES := src/patricia.cpp src/fathom/src/tbprobe.c

# Default compiler (MSYS2/MinGW มักมี clang++ ให้)
CXX := clang++
CXXFLAGS := -O3 -std=c++20 -ffast-math
LINKER :=

ifeq ($(OS), Windows_NT)
	DETECTED_OS := Windows
	SUFFIX := .exe
else
	DETECTED_OS := $(shell uname -s)
	LINKER := -lm
	CXXFLAGS += -pthread
	SUFFIX :=
endif

OUT := $(EXE)$(SUFFIX)

# ----------------------- Native build (generic) ---------------------------
build: $(SOURCES)
	$(CXX) $^ $(CXXFLAGS) -o $(OUT) $(LINKER)

default: build

# ----------------------- Datagen ------------------------------------------
datagen: datagen/datagen.cpp
	$(CXX) $^ $(CXXFLAGS) -o data$(SUFFIX) $(LINKER)

# ----------------------- AVX2 build ---------------------------------------
# เอาไว้ทำไบนารีเฉพาะเครื่องที่รองรับ AVX2
avx2: CXX := clang++
avx2: CXXFLAGS := -O3 -std=c++20 -ffast-math -mavx2 -mfma -msse4.2 -mpopcnt
avx2: OUT := $(EXE)_avx2$(SUFFIX)
avx2: $(SOURCES)
	$(CXX) $^ $(CXXFLAGS) -o $(OUT) $(LINKER)

# ----------------------- AVX2 + BMI2 build --------------------------------
# แรงสุด (ต้องรองรับ AVX2 + BMI2)
avx2_bmi2: CXX := clang++
avx2_bmi2: CXXFLAGS := -O3 -std=c++20 -ffast-math -mavx2 -mbmi -mbmi2 -mfma -msse4.2 -mpopcnt
avx2_bmi2: OUT := $(EXE)_avx2_bmi2$(SUFFIX)
avx2_bmi2: $(SOURCES)
	$(CXX) $^ $(CXXFLAGS) -o $(OUT) $(LINKER)

# ----------------------- Native AArch64 build (on ARM host) ----------------
aarch64: CXX := clang++
aarch64: CXXFLAGS := -O3 -std=c++20 -ffast-math -mcpu=native -pthread -static-libstdc++ -static-libgcc
aarch64: OUT := $(EXE)_aarch64$(SUFFIX)
aarch64: $(SOURCES)
	$(CXX) $^ $(CXXFLAGS) -o $(OUT) $(LINKER)

# ----------------------- Cross-compile for AArch64 (fully static) ----------
# Usage: make cross-aarch64 CROSS=aarch64-linux-gnu
cross-aarch64: CROSS ?= aarch64-linux-gnu
cross-aarch64: CXX := $(CROSS)-g++
cross-aarch64: CXXFLAGS := -O3 -std=c++20 -ffast-math -march=armv8-a -pthread -static -static-libstdc++ -static-libgcc
cross-aarch64: OUT := $(EXE)_aarch64$(SUFFIX)
cross-aarch64: $(SOURCES)
	$(CXX) $(CXXFLAGS) $^ -o $(OUT)

# ----------------------- Cross-compile for AArch64 using clang -------------
# requires proper sysroot
cross-aarch64-clang: CXX := clang++
cross-aarch64-clang: CXXFLAGS := --target=aarch64-linux-gnu -O3 -std=c++20 -ffast-math -march=armv8-a -pthread -static
cross-aarch64-clang: OUT := $(EXE)_aarch64$(SUFFIX)
cross-aarch64-clang: $(SOURCES)
	$(CXX) $(CXXFLAGS) $^ -o $(OUT) $(LINKER)
SUFFIX := .exe
OUT := $(EXE)$(SUFFIX)


# ----------------------- Clean --------------------------------------------
clean:
	rm -f $(EXE)$(SUFFIX) $(EXE)_avx2$(SUFFIX) $(EXE)_avx2_bmi2$(SUFFIX) data$(SUFFIX)

