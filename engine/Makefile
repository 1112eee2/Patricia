# =========================
# Patricia - engine Makefile
# =========================

.PHONY: default build native avx2 avx2_bmi2 datagen clean strip info

# --- Output base name ---
EXE      := patricia

# --- Sources (engine folder is working dir) ---
SOURCES  := src/patricia.cpp src/fathom/src/tbprobe.c

# --- Toolchain defaults ---
CXX      ?= clang++
STRIP    ?= strip

# --- OS detection & suffix/linker flags ---
ifeq ($(OS),Windows_NT)
	DETECTED_OS := Windows
	SUFFIX      := .exe
	# Statically link libgcc/libstdc++ and winpthreads to avoid missing DLLs on Windows.
	LINKER      := -static-libgcc -static-libstdc++ -Wl,-Bstatic -lwinpthread -Wl,-Bdynamic
	# On MinGW/Clang Windows, pthread flags not needed usually, but harmless if present.
	THREADFLAGS :=
else
	DETECTED_OS := $(shell uname -s)
	SUFFIX      :=
	LINKER      := -lm
	THREADFLAGS := -pthread
endif

# --- Common flags ---
# Keep it portable for CI: don't force -march=native unless target is "native".
BASE_CXXFLAGS := -O3 -std=c++20 -ffast-math $(THREADFLAGS)

# Default output name
OUT := $(EXE)$(SUFFIX)

default: build

# -------------------------
# Generic build (portable)
# -------------------------
build: OUT := $(EXE)$(SUFFIX)
build: CXXFLAGS := $(BASE_CXXFLAGS)
build: $(SOURCES)
	$(CXX) $^ $(CXXFLAGS) -o $(OUT) $(LINKER)

# ---------------------------------------
# Native build (uses host CPU features)
# ---------------------------------------
native: OUT := $(EXE)_native$(SUFFIX)
native: CXXFLAGS := $(BASE_CXXFLAGS) -march=native
native: $(SOURCES)
	$(CXX) $^ $(CXXFLAGS) -o $(OUT) $(LINKER)

# -------------------------
# AVX2 build
# -------------------------
avx2: OUT := $(EXE)_avx2$(SUFFIX)
avx2: CXXFLAGS := $(BASE_CXXFLAGS) -mavx2 -mfma -msse4.2 -mpopcnt
avx2: $(SOURCES)
	$(CXX) $^ $(CXXFLAGS) -o $(OUT) $(LINKER)

# -----------------------------------
# AVX2 + BMI2 build (what you want)
# -----------------------------------
avx2_bmi2: OUT := $(EXE)_avx2_bmi2$(SUFFIX)
avx2_bmi2: CXXFLAGS := $(BASE_CXXFLAGS) -mavx2 -mbmi -mbmi2 -mfma -msse4.2 -mpopcnt
avx2_bmi2: $(SOURCES)
	$(CXX) $^ $(CXXFLAGS) -o $(OUT) $(LINKER)

# -------------------------
# Datagen tool build
# -------------------------
datagen: OUT := data$(SUFFIX)
datagen: CXXFLAGS := $(BASE_CXXFLAGS)
datagen: datagen/datagen.cpp
	$(CXX) $^ $(CXXFLAGS) -o $(OUT) $(LINKER)

# -------------------------
# Strip (reduce binary size)
# Usage: make strip OUT=patricia_avx2_bmi2.exe
# -------------------------
strip:
	$(STRIP) -s "$(OUT)"

# -------------------------
# Clean
# -------------------------
clean:
	-@rm -f "$(EXE)$(SUFFIX)" \
		"$(EXE)_native$(SUFFIX)" \
		"$(EXE)_avx2$(SUFFIX)" \
		"$(EXE)_avx2_bmi2$(SUFFIX)" \
		"data$(SUFFIX)" 2>/dev/null || true

# -------------------------
# Info
# -------------------------
info:
	@echo OS=$(DETECTED_OS)
	@echo CXX=$(CXX)
	@echo STRIP=$(STRIP)
	@echo SOURCES=$(SOURCES)
	@echo BASE_CXXFLAGS=$(BASE_CXXFLAGS)
	@echo LINKER=$(LINKER)
	@echo SUFFIX=$(SUFFIX)
