# FlexFlow Installation
FlexFlow can be built from source code using the following instructions.

# 1. Download the source code
* Clone the FlexFlow source code from the github.
```
git clone --recursive https://github.com/flexflow/FlexFlow.git
```

## FlexFlow Python dependencies
* The FlexFlow Python support requires several additional Python libraries, please check [this](https://github.com/flexflow/FlexFlow/blob/master/python/requirements.txt) for details. 
We recommend to use `pip` or `conda` to install the dependencies. 

Note: all Python dependencies will be automatically installed if install the FlexFlow Python Interface using the PyPi repository (see the Installation below).

# 2. Build the FlexFlow
## 2.1 Makefile
### Build dependent libraries

* Build the NCCL library. (If using NCCL for parameter synchronization. )
```
cd nccl
make -j src.build NVCC_GENCODE="-gencode=arch=compute_XX,code=sm_XX"
```
Replace XX with the compatibility of your GPU devices (e.g., 70 for Volta GPUs and 60 for Pascal GPUs).

But you could also install it via `apt` if your system supports it:
```
sudo apt install libnccl-dev
```

### Build FlexFlow runtime with C++ interface
The `FF_HOME` environment variable is used for building and running FlexFlow. You can add the following line in `~/.bashrc`.
```
export FF_HOME=/path/to/FlexFlow
```
The path should point to where you cloned this repository.

Use the following command line to build a DNN model (e.g., InceptionV3). See the [examples](examples) folders for more existing FlexFlow applications.
```
./ffcompile.sh examples/InceptionV3
```

### Build FlexFlow Runtime with Python Interface (C++ interface is also enabled)

* Set the following environment variables. For `CUDNN_HOME`, you should be able to find `cudnn.h` under `CUDNN_HOME/include` and `libcudnn.so` under `CUDNN_HOME/lib` or `CUDNN_HOME/lib64`.
```
export FF_HOME=/path/to/FlexFlow
export CUDNN_HOME=/path/to/cudnn
```
To expedite the compilation, you can also set the `GPU_ARCH` environment variable to be the compatibility of your GPU devices (e.g., 70 for Volta GPUs and 60 for Pascal GPUs).
```
export GPU_ARCH=your_gpu_arch
```
If you have different cards, pass them all via comma, e.g.:

```
export GPU_ARCH=70,86
```

If Legion can not automatically detect your Python installation, you need to tell Legion manually by setting the `PYTHON_EXE`, `PYTHON_LIB` and `PYTHON_VERSION_MAJOR`, please refer to the `python/Makefile` for details

* Build the flexflow python executable using the following command line
```
cd python
make 
```

## 2.2 CMake

### Build dependent libraries
```
cd flexflow-third-party
mkdir build
cd build
cmake ../ -DCUDA_ARCH=xx -DPYTHON_VERSION=3.x (replace the xx with the corrected number)
make
sudo make install
```
Note: CMake sometimes can not automatically detect the correct `CUDA_ARCH`, so please set `CUDA_ARCH` if CMake can not detect it. 

### Build the FlexFlow
```
cd FlexFlow
cd config
```

The `config.linux` is an example of how to set the varibles required for CMake build. Please modify `CUDA_ARCH`, `CUDNN_DIR`, `LEGION_DIR` and `PROTOBUF_DIR` according to your environment.  `LEGION_DIR` and `PROTOBUF_DIR` are the installation directories of Legion and Protocol Buffer, not the source code directories.

Once the variables in the `config.linux` is set correctly, go to the home directory of FlexFlow, and run
```
mkdir build
cd build
../config/config.linux
make
```

# 3. Test the FlexFlow
* Set the `FF_HOME` environment variable before running the FlexFlow. You can add the following line in ~/.bashrc.
```
export FF_HOME=/path/to/FlexFlow
```

* Run FlexFlow Python examples
The C++ examples are in the [examples/python](https://github.com/flexflow/FlexFlow/tree/master/examples/python). 
For example, the AlexNet can be run as:
```
cd python
./flexflow_python example/python/native/alexnet.py -ll:py 1 -ll:gpu 1 -ll:fsize size of gpu buffer -ll:zsize size of zero buffer
``` 
The script of running all the Python examples is `python/test.sh`

* Run FlexFlow C++ examples

The C++ examples are in the [examples/cpp](https://github.com/flexflow/FlexFlow/tree/master/examples/cpp). 
For example, the AlexNet can be run as:
```
./alexnet -ll:gpu 1 -ll:fsize size of gpu buffer -ll:zsize size of zero buffer
``` 

# 4. Install the FlexFlow

* Install the FlexFlow binary, header file and library if using CMake. 
```
cd build
make install
```

* Install the FlexFlow Python interface using pip
If install from local:
```
cd python
pip install .
```

If install from the PyPI repository
```
pip install flexflow
```
All Python depencies will be automatically installed. 
