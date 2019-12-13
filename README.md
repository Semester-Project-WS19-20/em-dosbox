# Approach

For our Semesterproject "porting games to webassembly" we decided to port a whole emulator to run games on it. Therefore we forked the em-dosbox from dreamlayers (https://github.com/dreamlayers/em-dosbox.git) and adapted it for our purposes.


# Using DOSBox via webassembly - prerequisites

* Get urself the neccessary tools to port to webassembly, follow https://webassembly.org/getting-started/developers-guide/
* Git is usefull but you don't have to use it
* You need CMake for the `<make>` process
* A compiler like GCC could be neccessary
* You need Python 2.7.x, be shure to have it parallel to Python 3.x


# Getting DOSBox to work

* According to https://github.com/dreamlayers/em-dosbox/issues/49 the following changes to `<./src/Makefile.am>` where made:
  * line 21
    * change to `<dosbox_LDFLAGS=-s TOTAL_MEMORY=134217728 -s ALLOW_MEMORY_GROWTH=0 **-s FORCE_FILESYSTEM=1**>`
  * line 32
    * change to <dosbox_LDFLAGS+=-s WASM=1 **-s 'BINARYEN_TRAP_MODE="clamp"'**>
* Following the instructions for compiling from the README-dream
  * ran `<./autogen.sh>`
  * ! For the next step the emscripten toolchain is needed, if not available run `<source ./emsdk_env.sh --build=Release>` in emsdk folder
  * ran `<emconfigur ./configure --enable-wasm --disable-sync>`
  * ran `<make>` to build

