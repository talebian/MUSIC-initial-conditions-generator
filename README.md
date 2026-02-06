# MUSIC-initial-conditions-generator - Beginner's Guide
It is my first experience in using MUSIC code to generate initial condition for cosmological simulations.

<div align="center">

[<img src="https://www-n.oca.eu/ohahn/MUSIC/music_logo.png" 
      alt="MUSIC" 
      width="350">](https://www-n.oca.eu/ohahn/MUSIC/)

</div>
      
> **Note**: This repository documents my first experience using the MUSIC code for cosmological simulations. 
> As a beginner, I'm sharing my learning journey to help others navigate the setup and configuration process. you also can find a full user's guide on the original [MUSIC Website](https://www-n.oca.eu/ohahn/MUSIC/).

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
The result would be something like this:

```bash

    __    __     __  __     ______     __     ______      
   /\ "-./  \   /\ \/\ \   /\  ___\   /\ \   /\  ___\  
   \ \ \-./\ \  \ \ \_\ \  \ \___  \  \ \ \  \ \ \____ 
    \ \_\ \ \_\  \ \_____\  \/\_____\  \ \_\  \ \_____\ 
     \/_/  \/_/   \/_____/   \/_____/   \/_/   \/_____/ 

                            this is music! version 1.53

 - Version built from git rev.: N/A, tag: N/A, branch: N/A
 - Version was compiled for double precision.


 - Opening log file '../ics_axel.conf_log.txt'.
 - Using k-space sampled transfer functions...
 - Selecting transfer function plug-in 'eisenstein'...
 - starting at a=0.0196078
 - Selecting region generator plug-in 'box'...
terminate called after throwing an instance of 'config_file::ErrItemNotFound'
  what():  'output/format' not found.
Aborted (core dumped)
```
So you need to change 'ics_example.conf' by
```bash
vim ../ics_axel.conf

[output]
##generic MUSIC data format (used for testing)
##requires HDF5 installation and HDF5 enabled in Makefile
format                  = generic
filename                = debug.hdf5
```
to define the output configuration and output file!

## Output
The output file was created inside build directory: 'debug.hdf5'

## References
- [MUSIC Website](https://www-n.oca.eu/ohahn/MUSIC/)
- [User's Guide PDF](https://bitbucket.org/ohahn/music/downloads/MUSIC_Users_Guide.pdf)
- [Original Paper](http://arxiv.org/abs/1103.6031)

## Analyzing HDF5 Output with Python

```python

import h5py
import numpy as np
import matplotlib.pyplot as plt

def analyze_output(filename):
    """Analyze MUSIC output file"""
    
    with h5py.File(filename, 'r') as f:
        print("File structure:")
        def print_structure(name, obj):
            if isinstance(obj, h5py.Dataset):
                print(f"  Dataset: {name}, Shape: {obj.shape}, Dtype: {obj.dtype}")
            elif isinstance(obj, h5py.Group):
                print(f"  Group: {name}")
        
        f.visititems(print_structure)
        
        # Read particle data if available
        if 'PartType1' in f:
            particles = f['PartType1']
            print("\nParticle data:")
            for key in particles.keys():
                data = particles[key][:]
                print(f"  {key}: shape={data.shape}")
                
                if key == 'Coordinates':
                    print(f"    Range: [{data.min():.3f}, {data.max():.3f}]")
        
        # Read header information
        if 'Header' in f:
            header = f['Header'].attrs
            print("\nCosmological parameters:")
            for key in header:
                print(f"  {key}: {header[key]}")
```

## Visualization Script
```python
def visualize_particles(filename, n_samples=10000):
    """Visualize particle distribution"""
    
    with h5py.File(filename, 'r') as f:
        if 'PartType1' in f:
            coords = f['PartType1']['Coordinates'][:]
            
            # Sample for faster visualization
            if len(coords) > n_samples:
                indices = np.random.choice(len(coords), n_samples, replace=False)
                coords = coords[indices]
            
            # Create 3D visualization
            fig = plt.figure(figsize=(12, 8))
            
            # 3D plot
            ax1 = fig.add_subplot(221, projection='3d')
            ax1.scatter(coords[:,0], coords[:,1], coords[:,2], 
                       s=1, alpha=0.5)
            ax1.set_xlabel('X')
            ax1.set_ylabel('Y')
            ax1.set_zlabel('Z')
            ax1.set_title('3D Particle Distribution')
            
            # 2D projections
            projections = [('XY', 0, 1), ('XZ', 0, 2), ('YZ', 1, 2)]
            for i, (title, x_idx, y_idx) in enumerate(projections, 2):
                ax = fig.add_subplot(2, 2, i)
                ax.scatter(coords[:,x_idx], coords[:,y_idx], 
                          s=1, alpha=0.5)
                ax.set_xlabel(['X', 'X', 'Y'][i-2])
                ax.set_ylabel(['Y', 'Z', 'Z'][i-2])
                ax.set_title(f'{title} Projection')
                ax.grid(True, alpha=0.3)
            
            plt.tight_layout()
            plt.savefig('particle_visualization.png', dpi=150)
            plt.show()
```
