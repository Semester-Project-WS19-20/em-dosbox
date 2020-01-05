# Porting games to webasembly

For our Semesterproject "porting games to webassembly" we decided to port a whole emulator to run games on it. Therefore we forked the em-dosbox from dreamlayers (https://github.com/dreamlayers/em-dosbox.git) and adapted it for our purposes.


## Getting Startet

We recommend using ubuntu 16.04LTS

### Prerequisites
* Get git to clone this repository and the emscripten repository
* Get yourself the neccessary tools to port to webassembly, follow https://webassembly.org/getting-started/developers-guide/
  * If you are using the latest toolchain you will run into an error ```BINARYEN_TRAP_MODE is not supported by the LLVM wasm backend``` therefore we use the fastcomp backend `1.39.5-fastcomp`
  * Get the fastcomp emscripten toolchain for your system
    * for ubuntu 16.04LTS do the following
      * git clone https://github.com/emscripten-core/emsdk.git
      * go to your emsdk folder
      * intead of running ```./emsdk install latest``` run ```./emsdk install latest-fastcomp```
      * same goes for ```./emsdk activate latest``` run ```./emsdk activate latest-fastcomp```
      * run ```source ./emsdk_env.sh --build=Release```
  * You need CMake for the ```make``` process
  * A compiler like GCC could be neccessary
  * You need Python 2.7.x, be shure to have it parallel to Python 3.x


### Installing
#### DOSBox in webasembly
* Clone our repository em-dosbox with git
* Following the instructions from `README-dream`
  * ! make sure emscritpen toolchain is available, if not go to emsdk folder and run ```source ./emsdk_env.sh --build=Release```
  * go to your dosbox folder
  * run ```./autogen.sh```
  * if you get the error ```./autogen.sh: 8: ./autogen.sh: aclocal: not found``` you need automake as follows
    * run ```apt-get install autotools-dev```
    * run ```apt-get install automake```
    * run ```./autogen.sh``` again
  * run ```emconfigure ./configure --enable-wasm --disable-sync```
  * run ```make``` to build

## Running the tests
* in your /dosbox/src/ folder run ```emrun --no_browser --port 8080 .```
* open a browser
* go to `localhost:8080/`
* click on `dosbox.html`

## Run Games
In this repository we included the following games:
* rogue - freeware
* c-dogs - freeware
* Z - playable Demo
* Beneath a steel sky (BASS) - full version

To get the neccessary HTML files to run the games, following the descriptions in `README-dream` in the topic `Running DOS Programms` do as follows:
* package the gamefiles and create the .html and .data files to run with DOSBox
  * for rogue run ```./packager.py rogue rogue ROGUE.EXE```
  * for c-dogs run ```./packager.py c-dogs c-dogs CDOGS.EXE```
  * for Z run ```./packager.py z z Z.BAT```
  * for BASS run ```./packager.py steelsky steelsky SKY.EXE```
* We added the .html and .data files of each game to the .gitignore file so we won't commit them
* To test if the html files are working use the web server from the emscripten SDK:
  * run ```emrun --no_browser --port 8080 .``` in your `/dosbox/src/` directory
  * click on the `.html` file of the game

### Problems
There are several unsolved problems with the games:
* rogue
  * keymapping doesn't work correctly, neither german nor english layout matches
* c-dogs
  * game works but game velocity is way to high, if you start a game you loose instantly
* z
  * demo starts normal but without sound
* steelsky
  * unable to start game, "Error reading data disk"

### Suggested Solutions
To fix discribed problems it might work to use individual `dosbox.conf` files for each game as follows:
* get a copy of the general `dosbox.conf` file, source actually not known
* adjust the `dosbox.conf` file to fix problems
* place the individual `dosbox.conf` file in the game folder e.g. src/rogue/
* rerun `./packager.py` for each game
* test results

## Run your own games
If you want to run other games do as follows:
* Get the gamedata for your game (we got ours from https://www.dosgamesarchive.com/)
* Extract the gamefiles into your `/dosbox/src/` folder
* Get an `dosbox.conf` file for your game, or create one and add it to the gamefolder
* Proceed with packaging from **Run Games**


## Changes to dreamlayers DOSBox

Here are the changes we made to the dreamlayers DOSBox as we encountered some errors
 
* According to https://github.com/dreamlayers/em-dosbox/issues/49 the following changes to ```./src/Makefile.am``` where made:
  * line 21
    * changed to _dosbox_LDFLAGS=-s TOTAL_MEMORY=134217728 -s ALLOW_MEMORY_GROWTH=0 **-s FORCE_FILESYSTEM=1**_
  * line 32
    * changed to _dosbox_LDFLAGS+=-s WASM=1 **-s 'BINARYEN_TRAP_MODE="clamp"'**_
* With the HTML5 API update we ran into the error `exception thrown: SyntaxError: '' is not a valid selector` to fix this annother change in ```./src/Makefile.am``` was neccessary, see also https://github.com/emscripten-core/emscripten/issues/8732
  * line 21 changed to _dosbox_LDFLAGS=-s TOTAL_MEMORY=134217728 -s ALLOW_MEMORY_GROWTH=0 -s FORCE_FILESYSTEM=1 **-s DISABLE_DEPRECATED_FIND_EVENT_TARGET_BEHAVIOR=0**_


## Authors

* Benjamin Schrickel - Initial work - [dreamlayers](https://github.com/dreamlayers)

## License

This project, except the gamefiles, is licensed under the GNU GENERAL PUBLIC LICENSE see [COPYING](https://github.com/Semester-Project-WS19-20/em-dosbox/blob/em-dosbox-svn-sdl2/COPYING)

## Acknowledgments

* [Shrikant Lavhate](https://kerneltalks.com/troubleshooting/how-to-resolve-aclocal-not-found-error-in-ubuntu/) for the missing aclocal fix
* [TommyGER](https://github.com/dreamlayers/em-dosbox/issues/49) for the fix to make games run
* [gabrielcuvillier](https://github.com/emscripten-core/emscripten/issues/8732) for the HTML5 API fix
