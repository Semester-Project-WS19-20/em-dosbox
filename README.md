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
  * ran `<emconfigure ./configure --enable-wasm --disable-sync>`
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
* To test if the html files are working we used the web server from the emscripten SDK:
  * ran `<emrun --no_browser --port 8080 .`> in the html directory

## Problems

Following these steps we encountered some problems with each game as follows:
* rogue
  * keymapping doesn't work correctly, neither german nor english layout matches
* c-dogs
  * game works but game velocity is way to high, if you start a game you loose instantly
* z
  * demo starts normal but without sound
* steelsky
  *unable to start game, "Error reading data disk"

## Solutions

To fix discribed problems we are trying to use individual `<dosbox.conf>` files for each game as follows:
* get a copy of the general `<dosbox.conf>` file, source actually not known
* adjust the `<dosbox.conf>` file to fix problems
* place the individual `<dosbox.conf>` file in the game folder e.g. src/rogue/
* rerun `<./packager.py>` for each game
* test results
