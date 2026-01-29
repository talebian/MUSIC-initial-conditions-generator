# MUSIC-initial-conditions-generator - Beginner's Guide
It is my first experience in using MUSIC code to generate initial condition for cosmological simulations.

[![MUSIC](https://www-n.oca.eu/ohahn/MUSIC/music_logo.png)](https://www-n.oca.eu/ohahn/MUSIC/)

> **Note**: This repository documents my first experience using the MUSIC code for cosmological simulations. 
> As a beginner, I'm sharing my learning journey to help others navigate the setup and configuration process.

## Why I Created This Guide
When I first started using MUSIC, I found the learning curve quite steep. This repository aims to create a reference for my future work and for other beginners.

## Introduction
MUSIC (Multi-Scale Initial Conditions) is a powerful program for generating nested grid initial conditions for high-resolution "zoom" cosmological simulations. This code enables researchers to create sophisticated initial conditions for studying structure formation in the universe, with particular focus on high-resolution regions embedded within larger cosmological volumes.

## Clone
I cloned the source code using

```bash
git clone https://bitbucket.org/ohahn/music.git
```

## Building MUSIC

I created a build directory,  as follows; inside the music directory, 
```bash 
cd music
mkdir build
```
When I just create a new folder, then I configured the code using CMake
```bash
cd build
cmake ..
make
```

## Running
There is an example parameter file 'ics_example.conf' in the main directory. I just run it as a simple argument, e.g. from within the build directory:
```bash
./MUSIC ../ics_example.conf
```
## Output
The output file was created inside build directory: 'debug.hdf5'

