# QtCuda
using cuda in Qt, Linux/Windows

## cuda in ubuntu

=== 20190815 === 

Background: 
* Ubuntu 18.04
* Qt 5.7
* Qt Creater 4.0.2

start to install cuda in ubuntu, run it successfully.


## cuda in windows with VS

=== 20210531 ===

Background:
* Windows 10 x64
* Qt 5.14.2
* Qt Creater 4.11.1
* Cuda v11.1
* Visual Studio 2017

**Fail** to compile in Qt with **MinGW** compiler

start to use cuda in windows, run it **successfully** only if using **Visual Studio** as compiler

need to add the following in .pro

* MT/MD config as in VS
* nvcc -ccbin **"path of your cl.exe"**

```
win32 {
QMAKE_LIBDIR += $$CUDA_DIR/lib/x64/

CONFIG(debug, debug|release) {
    QMAKE_CXXFLAGS_DEBUG += /MTd
}

CONFIG(release, debug|release) {
    QMAKE_CXXFLAGS_RELEASE += /MT
}
}

win32 {
# Configuration of the Cuda compiler
CONFIG(debug, debug|release) {
    # Debug mode
    cuda_d.input = CUDA_SOURCES
    cuda_d.output = $$CUDA_OBJECTS_DIR/${QMAKE_FILE_BASE}_cuda.o
    cuda_d.commands = $$CUDA_DIR/bin/nvcc -D_DEBUG $$NVCC_OPTIONS $$CUDA_INC $$NVCC_LIBS --machine $$SYSTEM_TYPE \
-ccbin "C:/Program Files (x86)/Microsoft Visual Studio/2017/Community/VC/Tools/MSVC/14.16.27023/bin/Hostx64/x64" \
-arch=$$CUDA_ARCH -c -o ${QMAKE_FILE_OUT} ${QMAKE_FILE_NAME}
    cuda_d.dependency_type = TYPE_C
    QMAKE_EXTRA_COMPILERS += cuda_d
}
else {
    # Release mode
    cuda.input = CUDA_SOURCES
    cuda.output = $$CUDA_OBJECTS_DIR/${QMAKE_FILE_BASE}_cuda.o
    cuda.commands = $$CUDA_DIR/bin/nvcc $$NVCC_OPTIONS $$CUDA_INC $$NVCC_LIBS --machine $$SYSTEM_TYPE -arch=$$CUDA_ARCH -c -o ${QMAKE_FILE_OUT} ${QMAKE_FILE_NAME}
    cuda.dependency_type = TYPE_C
    QMAKE_EXTRA_COMPILERS += cuda
}

```

vectorAddDrv: 依赖于./data/vectorAdd_kernel64.ptx

=== 20210906 ===

.pro文件中：

**CUDA_DIR**应为Toolkit中的路径，而不是*C:\ProgramData\NVIDIA Corporation\CUDA Samples*中的路径

环境变量中的值为
```
CUDA_PATH=C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.1
CUDA_PATH_V11_1=C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.1
```

**CUDA_ARCH**应该和显卡型号对应（代码对应测试过的机器为NVIDIA GeForce GTX 1060 3GB, CUDA_ARCH = sm_50 ），否则能编译通过，但运行不会跑进cuda的函数中，也没有报错
