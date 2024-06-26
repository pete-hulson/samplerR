
# Name of executable.
OUTPUT:=sam
# Global source files from current dir.
SRCS:=$(wildcard *.tpl) 


# Path to ADMB distribution directory
# export ADMB_HOME=/Users/stevenmartell1/admb-trunk/build/dist
# export ADMB_HOME=/Users/jim/admb/build/dist

# Compiler
admb:=admb

# Compiler and linker flags.

# Release specific flags.
RELEASE_CXXFLAGS:=-f

# Debug specific flags.
DEBUG_CXXFLAGS:=-g -s

# Name of text file containing build number.
BUILD_NUMBER_FILE=build-number.txt

# ======================= END OF CONFIGURABLE THINGS ===========================
# Create debug & release list of object files as well as dep files.
BASEFILES:=$(basename $(SRCS))

# Default to release build.
all: $(OUTPUT) debug release

$(OUTPUT).cpp: $(OUTPUT).tpl
	$(ADMB_HOME)/bin/tpl2cpp $(OUTPUT)

# Directory targets
build/debug:
	@echo creating debug directory
	@mkdir -p build/debug build/deps

build/release:
	@echo creating release directory
	@mkdir -p build/release build/deps

# Debug route.
.PHONY: debug
debug: CXXFLAGS+= $(DEBUG_CXXFLAGS)
debug: LDFLAGS+= $(DEBUG_LDFLAGS)
debug: build/debug/$(OUTPUT)

build/debug/$(OUTPUT): build/debug $(DEBUG_OBJS)
	@echo 'linking ' build/debug/$(OUTPUT)
	@$(CXX) -o build/debug/$(OUTPUT) $(DEBUG_OBJS) $(LDFLAGS)

-include $(DEPFILES)

build/debug/%.o : %.cpp
	@echo 'compiling ' $<
	@$(CXX) -c $(CXXFLAGS) $< -o $@
	@$(CXX) -MM $(CXXFLAGS) $< -o build/deps/$*.d
	@mv -f build/deps/$*.d build/deps/$*.d.tmp
	@sed -e 's|.*:|build/debug/$*.o:|' < build/deps/$*.d.tmp \
	  > build/deps/$*.d
	@sed -e 's/.*://' -e 's/\\$$//' < build/deps/$*.d.tmp | fmt -1 \
	  | sed -e 's/^ *//' -e 's/$$/:/' >> build/deps/$*.d
	@rm -f build/deps/$*.d.tmp	

# Release route.
.PHONY: release
release: CXXFLAGS+= $(RELEASE_CXXFLAGS)
release: LDFLAGS+= $(RELEASE_LDFLAGS)
release: build/release/$(OUTPUT)

build/release/$(OUTPUT): build/release $(RELEASE_OBJS)
	@echo 'linking ' build/release/$(OUTPUT)
	@$(CXX) -o build/release/$(OUTPUT) $(RELEASE_OBJS) $(LDFLAGS)

build/release/%.o : %.cpp
	@echo 'compiling ' $<
	@$(CXX) -c $(CXXFLAGS) $< -o $@
	@$(CXX) -MM $(CXXFLAGS) $< -o build/deps/$*.d
	@mv -f build/deps/$*.d build/deps/$*.d.tmp
	@sed -e 's|.*:|build/release/$*.o:|' < build/deps/$*.d.tmp \
	  > build/deps/$*.d
	@sed -e 's/.*://' -e 's/\\$$//' < build/deps/$*.d.tmp | fmt -1 | \
	  sed -e 's/^ *//' -e 's/$$/:/' >> build/deps/$*.d
	@rm -f build/deps/$*.d.tmp

.PHONY: clean
clean:
	@echo 'removing build directory'
	@rm -rf build
