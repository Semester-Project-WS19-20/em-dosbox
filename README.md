# Approach

For our Semesterproject "porting games to webassembly" we decided to port a whole emulator to run games on it. Therefore we forked the em-dosbox from dreamlayers (https://github.com/dreamlayers/em-dosbox.git) and adapted it for our purposes.


# Getting startet

* Get git to clone this repository and the emscripten repository
* Get yourself the neccessary tools to port to webassembly, follow https://webassembly.org/getting-started/developers-guide/
  * Get the fastcomp emscripten toolchain for your system
    * for ubuntu 16.04LTS do the following
      * to do so intead of running ```./emsdk install latest``` run ```./emsdk install latest-fastcomp```
      * same goes for ```./emsdk activate latest``` run ```./emsdk activate latest-fastcomp```
  * You need CMake for the ```make``` process
  * A compiler like GCC could be neccessary
  * You need Python 2.7.x, be shure to have it parallel to Python 3.x


# Step by step guide to run games in ubuntu 16.04LTS

## Get a working DOSBox in webasembly
* Clone our repository em-dosbox https://github.com/Semester-Project-WS19-20/em-dosbox.git with git
* Following the instructions from `README-dream`
  * ! make sure emscritpen toolchain is available, if not go to emsdk folder and run ```source ./emsdk_env.sh --build=Release```
  * run ```./autogen.sh```
  * if you get the error ```./autogen.sh: 8: ./autogen.sh: aclocal: not found``` you need automake as follows
    * run ```apt-get install autotools-dev```
    * run ```apt-get install automake```
    * run ```./autogen.sh``` again
  * run ```emconfigure ./configure --enable-wasm --disable-sync```
  * run ```make``` to build

## Test your DOSBox
* in your dosbox folder run ```emrun --no_browser --port 8080 .```
* open a browser
* 

Next step is to run games on it. In this repository we have following games

* rogue - freeware
* c-dogs - freeware
* Z - playable Demo
* Beneath a steel sky (BASS) - full version 

If you want to run other DOS-Games 

# Changes to dreamlayers DOSBox to make games run

We tried using the instructions in the `README-dream`, but with it no game was running. After searching for a solution we followed the steps below to get the games to run with DOSBox.
 
* According to https://github.com/dreamlayers/em-dosbox/issues/49 the following changes to ```./src/Makefile.am``` where made:
  * line 21
    * changed to _dosbox_LDFLAGS=-s TOTAL_MEMORY=134217728 -s ALLOW_MEMORY_GROWTH=0 **-s FORCE_FILESYSTEM=1**_
  * line 32
    * changed to _dosbox_LDFLAGS+=-s WASM=1 **-s 'BINARYEN_TRAP_MODE="clamp"'**_


# Running Games

To get the neccessary HTML files to run the games, we followed the descriptions in `README-dream` in the topic `Running DOS Programms`

Here is what we did step by step:
* First we got some DOSGames from https://www.dosgamesarchive.com/ namely:
  * rogue - freeware
  * c-dogs - freeware
  * Z - playable Demo
  * Beneath a steel sky (BASS) - full version
* Extracted the files as follows:
  * ran ```unzip rogue.zip -d ~/project-dosbox/src/rogue/```
  * ran ```unzip c-dogs.zip -d ~/project-dosbox/src/c-dogs/```
  * ran ```unzip z.zip -d ~/project-dosbox/src/z/```
  * ran ```unzip steelsky.zip -d ~/project-dosbox/src/steelsky/```
* Next step is to package the gamefiles and create the .html and .data files to run with DOSBox. Therefore we used the `<packager.py>` as discribed:
  * ran ```./packager.py rogue rogue ROGUE.EXE```
  * ran ```./packager.py c-dogs c-dogs CDOGS.EXE```
  * ran ```./packager.py z z Z.BAT```
  * ran ```./packager.py steelsky steelsky SKY.EXE```
* We added the .html and .data files of each game to the .gitignore file so we won't commit them
* To test if the html files are working we used the web server from the emscripten SDK:
  * ran ```emrun --no_browser --port 8080 .``` in the html directory

## Problems

Following these steps we encountered some problems with each game as follows:
* rogue
  * keymapping doesn't work correctly, neither german nor english layout matches
* c-dogs
  * game works but game velocity is way to high, if you start a game you loose instantly
* z
  * demo starts normal but without sound
* steelsky
  * unable to start game, "Error reading data disk"

## Solutions

To fix discribed problems we are trying to use individual `dosbox.conf` files for each game as follows:
* get a copy of the general `dosbox.conf` file, source actually not known
* adjust the `dosbox.conf` file to fix problems
* place the individual `dosbox.conf` file in the game folder e.g. src/rogue/
* rerun `./packager.py` for each game
* test results
