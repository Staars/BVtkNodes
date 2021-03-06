# BVTKNodes VTK compilation for Linux 

These instructions were tested with bash on Ubuntu 16.04, Blender
2.80 beta and VTK 8.2.0. **Note:** You must know how to install prerequisites
and basics of source code compiling to follow these instructions.
Compilation steps are

1. Compile Python matching the Python version of Blender
2. Compile VTK and with python bindings
3. Configure environment variables to make Blender use self compiled Python and VTK
4. Start Blender

In this example, it is assumed that Python and VTK are installed to following folders.
You may replace ```~/BVTKNodes``` with another installation folder if you want.
```
~/BVTKNodes/Python-3.7.0
~/BVTKNodes/VTK-8.2.0
```

Note: You can find out Blender's Python version by running following command
in Blender Python Console

```
import sys 
sys.version
```

Blender 2.80 beta uses Python version 3.7.0.

Note: It is assumed that all commands below are run in sequence in same
terminal window (to keep environment variables in line).

# 1. Compile Python

Download and compile Python
```
cd ~/BVTKNodes/Python-3.7.0
wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tgz
tar xvzf Python-3.7.0.tgz
mkdir install
cd install
MYPYTHON=$PWD
cd ../Python-3.7.0
./configure --prefix=$MYPYTHON --with-computed-gotos --with-pymalloc --enable-shared
make -j 4
make install
export PYTHONPATH=$MYPYTHON/lib/python3.7/site-packages:$PYTHONPATH
export LD_LIBRARY_PATH=$MYPYTHON/lib:$LD_LIBRARY_PATH
export PATH=$MYPYTHON/bin:$PATH
```

# 2. Compile VTK

Download and compile VTK

```
cd ~/BVTKNodes/VTK-8.2.0
wget https://www.vtk.org/files/release/8.2/VTK-8.2.0.tar.gz
tar xzvf VTK-8.2.0.tar.gz
mkdir build
cd build
ccmake ../VTK-8.2.0 -DCMAKE_INSTALL_PREFIX:PATH=$MYPYTHON -DCMAKE_BUILD_TYPE:STRING=Release -DModule_vtkImagingOpenGL2:BOOL=ON -DModule_vtkPython:BOOL=ON -DModule_vtkWrappingPythonCore:BOOL=ON -DPYTHON_INCLUDE_DIR:PATH=$MYPYTHON/include/python3.7m -DPYTHON_LIBRARY:FILEPATH=$MYPYTHON/lib/libpython3.7m.so -DVTK_PYTHON_VERSION:STRING=3 -DVTK_WRAP_PYTHON:BOOL=ON
```

In ccmake, press
- **c** to configure
- **c** to configure again
- **g** to generate config files and exit

```
make -j 4
make install
```

# 3. Configure environment variables

I create a script which configures environment variables and starts
Blender. Another option is to add export commands to ```~/.bashrc```.
Replace ```/path/to/start/blender``` with correct path to blender
binary.

```
cat >~/start_bvtknodes <<EOF
export PYTHONPATH=$MYPYTHON/lib/python3.7/site-packages:$PYTHONPATH
export LD_LIBRARY_PATH=$MYPYTHON/lib:$LD_LIBRARY_PATH
export PATH=$MYPYTHON/bin:$PATH
/path/to/start/blender
EOF
chmod a+x ~/start_bvtknodes
```

# 4. Start Blender

In terminal, give command ```~/start_bvtknodes```

You can test if VTK is found in Blender Python Console with commands

```
import vtk
vtk.vtkVersion().GetVTKVersion()
```

which should return VTK version number 8.2.0.

