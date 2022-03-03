# Opticka: Behavioural Experiment Manager

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.592253.svg)](https://doi.org/10.5281/zenodo.592253)  —  [![Open in Visual Studio Code](https://open.vscode.dev/badges/open-in-vscode.svg)](https://open.vscode.dev/iandol/opticka)

Opticka is an object-oriented framework with optional GUI for the [Psychophysics toolbox (PTB)](http://psychtoolbox.org/), allowing full experimental presentation of complex visual or other stimuli. It is designed to work on Linux, macOS or Windows and interfaces via strobed words and ethernet for recording neurophysiological and behavioural data. Full behavioural control is available by use of a [Finite State-Machine](http://iandol.github.io/OptickaDocs/classstate_machine.html#details) controller, in addition to simple method of constants (MOC) experiments. Opticka uses the TCP interface for Eyelink & Tobii Pro eyetrackers affording better control, reliability and data recording over depending on analog voltage output (thus doesn't require a DAQ cards for eye data). The various base classes can be used _without_ the need to run the GUI (see [`optickatest.m`](http://iandol.github.io/OptickaDocs/optickatest.html) for an example), and plug-n-play stimuli provide a unified interface (`setup`, `animate`, `draw`, `update`, `reset`) to integrate into other PTB routines. The object methods take care of all the background geometry and normalisation, meaning stimuli are much easier to use than “raw” PTB commands alone. Analysis routines are also present for taking e.g. Plexon files (`.PL2` or `.PLX`), Eyelink files (`.EDF`), and behavioural responses and parsing them into a consistent structure, interfacing directly with [Fieldtrip](http://fieldtrip.fcdonders.nl/start) for further spike, LFP, and spike-LFP analysis. Opticka is more modular and affords much better graphics control (most stimuli are optimised OpenGL with advanced control via PTB) than [MonkeyLogic](http://www.brown.edu/Research/monkeylogic/).  

## Sample hardware setup

The diagram below shows a sample Opticka configuration setup. Note that the eyetracker, display, synchronisation and electrophysiology systems can be swapped for other hardware ([see the list below](#Hardware-currently-supported)). While I prefer using a Display++ or a DataPixx/ViewPixx to ensure temporal fidelity, you can use a LabJack or arduino for synchronisation (where a photodiode becomes more important):  

![Example hardware setup to run Opticka](https://github.com/iandol/opticka/raw/gh-pages/images/Opticka-Setup.png)

## GUI

A GUI can be used to control the hardware, stimuli and variables needed for both method of constant (MOC) and more complex behavioural tasks that use the [state machine](#state-machine-control).It is useful for cases where you need to change experiment or stimulus parameters quickly between runs, or for staff who are not programmers themselves. The GUI is **not required** to utilise the underlying classes…  

```matlab
o = opticka; %==run the GUI, returns an object 'o' for introspection from the command window...
```

![Opticka Screenshot](https://github.com/iandol/opticka/raw/gh-pages/images/opticka.png)

## State machine control

For more complex behavioural tasks, a state machine is used. You can still edit visual stimuli and task variables in the GUI, and then edit a `StateInfo.m` file that specifies states (like `paused`, `prefixation`, `stimulus`, `correct`, `breakfix` etc.) to be run and for each state which methods/functions are executed (cell arrays of functions that run on `ENTER`, `WITHIN` & `EXIT` of states). States can switch (`TRANSITION`) based on logic, for example a particular saccade or fixation or button responses to transition to a `correct` state… See an [example StateInfo.m file here](https://github.com/iandol/opticka/blob/master/DefaultStateInfo.m); and you can test a minimal generic state machine run using:

```matlab
sM = stateMachine;
runDemo(sM);
```

## Hardware currently supported:

* **Display & Digital I/O**: high quality display (high bit depths, great colour management) and microsecond precise frame-locked digital I/O: [Display++ by CRS](https://www.crsltd.com/tools-for-vision-science/calibrated-displays/displaypp-lcd-monitor/).  
* **Display & Digital I/O**: high quality display (high bit depths) and easy-to-use microsecond precise digital I/O: [DataPixx / ViewPixx / ProPixx](http://vpixx.com/products/tools-for-vision-sciences/).  
* **Display**: any normal monitor; remember that PTB can support 10bits and higher output, steroscopic display, HDR output etc.  
* **Digital I/O**: [LabJack](https://labjack.com/) USB U3/U6 or T4/T7 DAQs, strobed words up to 12bits. The T4/T7 are preferred as the I/O is asynchronous and work on all platforms.  
* **Digital I/O**: [Arduino]() boards for simple TTL triggers for reward systems, MagStim etc. In particular, digitial TTLs are asynchronous so they do not block the experimental loop. The [seeeduino Xiao](https://wiki.seeedstudio.com/Seeeduino-XIAO/) is small, cheap, fast and works really well, but the [Uno](https://docs.arduino.cc/hardware/uno-rev3) is also well supported.  
* **Eyetracking**: [Eyelink 1000]() -- uses the native ethernet link. This enables much better control, drawing stimuli and experiment values onto the eyelink screen. EDF files are stored and `eyelinkAnalysis.m` uses native EDF loading for full trial-by-trial analysis without conversion.  
* **Eyetracking**: [Tobii Pro Eyetrackers]() -- using the excellent [Titta toolbox](https://github.com/dcnieho/Titta) to manage calibration and recording. Tobii Pro eyetrackers do not require head fixation.  
* **Electrophysiology**: in theory any recording system that accepts digital triggers / strobed words; we have dedicated code for the Plexon Omniplex system. Opticka can use TCP communication over ethernet to transmit current variable data to allow online data visualisation (PSTHs etc. for each experiment variable) on the Omniplex machine. Digital triggers can be generated with good temporal fidelity.  
* **Visual Calibration**: we support use of a CRS SpectrolCal II (preferred but expensive) or ColorCal 2, or a VPixx i1Pro, or manual interfacing with most other photometers that PTB supports. The [calibrateLuminance]() class.  
* **Photodiode boxes**: we prefer TSL251R light-to-voltage photodiodes, which can be recorded directy into your electrophysiology system or can generate digital triggers via an [Arduino interface](https://github.com/iandol/opticka/tree/master/tools/photodiode).  

## Quick Documentation
`optickatest.m` is a minimal example showing a simple script-based method of constants (MOC) experiment with 11 different animated stimuli varying across angle, contrast and orientation. Read the Matlab-generated documentation here: [`optickatest.m` Report](http://iandol.github.io/OptickaDocs/optickaTest.html). More complex behavioural control (gaze-contingent experiments with variable logic per trial) utilise a state machine, see [`optickaBehaviourTest.m` Report](http://iandol.github.io/OptickaDocs/optickaBehaviourTest.html) for a simple script-based example. You can see further examples in the [CoreProtocols]() folder; these can be loaded into the GUI and you can also inspect the state machine StateInfo `.m` files.  

There is also auto-generated class documentation here: [Opticka Class Docs](http://iandol.github.io/OptickaDocs/inherits.html), that details the major classes and their methods and properties. This is generated from the comments in the code, which as always could be improved…  

## Basic Install Instructions
See [**Detailed instructions**](help/INSTALL.md) for full install details…

Opticka prefers the latest Psychophysics Toolbox (V3.0.17+) and at least Matlab 2017a (it uses object-oriented property validation introduced in that version). It has been tested on 64bit Ubuntu 20.04 & macOS 12.x with **MATLAB 2021b** (newer versions are generally faster). You can simply download the [GitHub ZIP File](https://github.com/iandol/opticka/archive/refs/heads/master.zip), unzip and CD to the folder and run `addOptickaToPath.m`. Or to keep easily up-to-date if you have git installed, clone this Github repo, CD to the folder then run `addOptickaToPath.m`.

Opticka currently works on Linux, macOS and Windows. The older LabJack U3/U6 interface currently only works under Linux and macOS; the LabJack T4/T7 does work cross-platform however. Linux is **by far** the best OS according the PTB developer Mario Kleiner, and receives the majority of development work from him, therefore it is *strongly advised* to use it for experiments. My experience is that Linux is much more robust and performant than macOS or Windows, and it is well worth the effort to use Linux for PTB experimental computers.

## Features
* Values are always given in eye-relevant co-ordinates (degrees etc.) that are internally calculated based on screen geometry/distance
* No limit on the number of independent variables, and variables can be linked to multiple stimuli.
* A state machine logic can run behavioural tasks driven by e.g. eye position or behavioural response. State machines can flexibly run tasks and chains of states define your experimental loop.
* Number of heterogeneous stimuli displayed simultaneously only limited by the GPU / computer power. Us of GPU procedural textures whereever possible ensure fast and efficient stimuli.
* Display lists are used, so one can easily change drawing order (i.e. what stimulus draws over other stimuli), by changing its order in the list.
* **Object-Oriented**, allowing stimulus classes to be easily added and code to auto-document using DOxygen.
* The set of stimuli and variables can be saved into protocol files, to easily run successive protocols quickly.
* Fairly comprehensive control of the PTB interface to the drawing hardware, like blending mode, bit depth, windowing, verbosity.
* Colour is defined in floating point format, takes advantage of higher bit depths in newer graphics cards when available. The buffer can be defined from 8-32bits, use full alpha blending within that space and enable a >8bit output using pseudogrey bitstealing techniques.
* Sub-pixel precision (1/256th pixel) for movement and positioning.
* TTL output to data acquisition and other devices. Currently uses DataPixx or LabJack to interface to the Plexon Omniplex using strobed words.
* Can communicate with other machines on the network during display using TCP/UDP (used e.g. to control a Plexon online display, so one can see PSTHs for each stimulus variable shown in real time).
* Each stimulus has its own relative X & Y position, and the screen centre can be arbitrarily moved via the GUI. This allows quick setup over particular parts of visual space, i.e. relative to a receptive field without needing to edit lots of other values.
* Can record stimuli to video files.
* Manages monitor calibration using SpectroCalII or ColorCalII from CRG or an i1Pro from ViewPixx. Calibration sets can be loaded, saved and plotted locally via the GUI.
* **Gratings** (**all** using procedural textures for high performance):
   * Per-frame update of properties for arbitrary numbers of grating patches.
   * Rectangular or circular aperture.
   * Cosine or hermite interpolation for filtering grating edges.
   * Square wave gratings, also using a procedural texture, i.e. very fast.
   * Gabors
* **Colour gratings**; using any two colors procedurally blended against a background colour.
* **Coherent dot stimuli**; coherence expressed from 0-1. Either square or round dots. Colours can be simple, random, random luminance or binary. Kill rates allow random replacement rates for dots. Circularly smoothed masked aperture option. Newsroom style dots with motion distributions etc.
* **Bars**; either solid colour or checkerboard / random noise texture. Bars can be animated, direction can be independent of their angle.
* **Flashing smoothed spots**.
* **Pictures/Images** that can drift and rotate.
* **Movies** that can be scaled and drift. Movie playback is double-buffered to allow them to work alongside other stimuli.
* **Hand-mapping module** - use mouse controlled dynamic bar / texture / colour to handmap receptive fields; includes logging of clicked position and later printout / storage of hand maps. These maps are in screen co-ordinates for quick subsequent stimulus placement.  

## Licence
Opticka is licenced under the LGPL3 open source licence.

