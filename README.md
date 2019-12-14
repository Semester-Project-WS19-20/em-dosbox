# Approach

For our Semesterproject "porting games to webassembly" we decided to port a whole emulator to run games on it. Therefore we forked the em-dosbox from dreamlayers (https://github.com/dreamlayers/em-dosbox.git) and adapted it for our purposes.


# Using DOSBox via webassembly - prerequisites

* Get yourself the neccessary tools to port to webassembly, follow https://webassembly.org/getting-started/developers-guide/
* Git is usefull but you don't have to use it
* You need CMake for the `<make>` process
* A compiler like GCC could be neccessary
* You need Python 2.7.x, be shure to have it parallel to Python 3.x


# Getting DOSBox to work

We tried using the instructions in the `README-dream`, but with it no game was running. After searching for a solution we followed the steps below to get the games to run with DOSBox.
 
* According to https://github.com/dreamlayers/em-dosbox/issues/49 the following changes to `<./src/Makefile.am>` where made:
  * line 21
    * changed to <dosbox_LDFLAGS=-s TOTAL_MEMORY=134217728 -s ALLOW_MEMORY_GROWTH=0 **-s FORCE_FILESYSTEM=1**>
  * line 32
    * changed to <dosbox_LDFLAGS+=-s WASM=1 **-s 'BINARYEN_TRAP_MODE="clamp"'**>
* Followed the instructions for compiling from the `README-dream`
  * ran `<./autogen.sh>`
  * ! For the next step the emscripten toolchain is needed, if not available run `<source ./emsdk_env.sh --build=Release>` in emsdk folder
  * ran `<emconfigur ./configure --enable-wasm --disable-sync>`
  * ran `<make>` to build


# Running Games

To get the neccessary HTML files to run the games, we followed the descriptions in `README-dream` in the topic `Running DOS Programms`

Here is what we did step by step:
* First we got some DOSGames from https://www.dosgamesarchive.com/ namely:
  * rogue - freeware
  * c-dogs - freeware
  * Z - playable Demo
  * Beneath a steel sky (BASS) - full version
* Extracted the files as follows:
  * ran `<unzip rogue.zip -d ~/project-dosbox/src/rogue/>`
  * ran `<unzip c-dogs.zip -d ~/project-dosbox/src/c-dogs/>`
  * ran `<unzip z.zip -d ~/project-dosbox/src/z/>`
  * ran `<unzip steelsky.zip -d ~/project-dosbox/src/steelsky/>`
* Next step is to package the gamefiles and create the .html and .data files to run with DOSBox. Therefore we used the `<packager.py>` as discribed:
  * ran `<./packager.py rogue rogue ROGUE.EXE>`
  * ran `<./packager.py c-dogs c-dogs CDOGS.EXE>`
  * ran `<./packager.py z z Z.BAT>`
  * ran `<./packager.py steelsky steelsky SKY.EXE>`
