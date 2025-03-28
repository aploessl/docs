# Build from source on Windows

Build a TensorFlow *pip* package from the source and install it on Windows.

Note: We already provide well-tested, pre-built
[TensorFlow packages](./pip.md) for Windows systems.

## Setup for Windows

Install the following build tools to configure your Windows development
environment.

### Install Python and the TensorFlow package dependencies

Install a
[Python 3.9+ 64-bit release for Windows](https://www.python.org/downloads/windows/).
Select *pip* as an optional feature and add it to your `%PATH%` environmental
variable.

Install the TensorFlow *pip* package dependencies:

<pre class="devsite-click-to-copy">
<code class="devsite-terminal tfo-terminal-windows">pip3 install -U pip</code>
<code class="devsite-terminal tfo-terminal-windows">pip3 install -U six numpy wheel packaging</code>
<code class="devsite-terminal tfo-terminal-windows">pip3 install -U keras_preprocessing --no-deps</code>
</pre>

The dependencies are listed in the
<a href="https://github.com/tensorflow/tensorflow/blob/master/tensorflow/tools/pip_package/setup.py" class="external"><code>setup.py</code></a>
file under `REQUIRED_PACKAGES`.

### Install Bazel

[Install Bazel](./source.md#install-bazel), the build tool used to compile
TensorFlow. For Bazel version, see the
[tested build configurations](#tested-build-configurations) for Windows.
Configure Bazel to
<a href="https://docs.bazel.build/versions/master/windows.html#build-c" class="external">build
C++</a>.

Add the location of the Bazel executable to your `%PATH%` environment variable.

### Install MSYS2

[Install MSYS2](https://www.msys2.org/) for the bin tools needed to
build TensorFlow. If MSYS2 is installed to `C:\msys64`, add
`C:\msys64\usr\bin` to your `%PATH%` environment variable. Then, using `cmd.exe`,
run:

<pre class="devsite-terminal tfo-terminal-windows devsite-click-to-copy">
pacman -Syu (requires a console restart)
pacman -S git patch unzip
pacman -S git patch unzip rsync
</pre>

Note: Clang will be the preferred compiler to build TensorFlow CPU wheels on the Windows Platform starting with TF 2.16.1 The currently supported version is LLVM/clang 17.0.6.

Note: To build with Clang on Windows, it is required to install both LLVM and Visual C++ Build tools as although Windows uses clang-cl.exe as the compiler, Visual C++ Build tools are needed to link to Visual C++ libraries

### Install Visual C++ Build Tools 2022

Install the *Visual C++ build tools 2022*. This comes with *Visual Studio Community 2022*
but can be installed separately:

1.  Go to the
    [Visual Studio downloads](https://visualstudio.microsoft.com/downloads/),
2.  Select *Tools for Visual Studio or Other Tools, Framework and Redistributables*,
3.  Download and install:
    -   *Build Tools for Visual Studio 2022*
    -   *Microsoft Visual C++ Redistributables for Visual Studio 2022*

Note: TensorFlow is tested against the *Visual Studio Community 2022*.

### Install LLVM

1.  Go to the
    [LLVM downloads](https://github.com/llvm/llvm-project/releases/),
2.  Download and install Windows-compatible LLVM in C:/Program Files/LLVM e.g., LLVM-17.0.6-win64.exe


### Install GPU support (optional)

See the Windows [GPU support](./gpu.md) guide to install the drivers and
additional software required to run TensorFlow on a GPU.

Note: GPU support on native-Windows is only available for 2.10 or earlier versions, starting in TF 2.11, CUDA build is not supported for Windows. For using TensorFlow GPU on Windows, you will need to build/install TensorFlow in WSL2 or use tensorflow-cpu with TensorFlow-DirectML-Plugin

### Download the TensorFlow source code

Use [Git](https://git-scm.com/) to clone the
[TensorFlow repository](https://github.com/tensorflow/tensorflow)
(`git` is installed with MSYS2):

<pre class="devsite-click-to-copy">
<code class="devsite-terminal tfo-terminal-windows">git clone https://github.com/tensorflow/tensorflow.git</code>
<code class="devsite-terminal tfo-terminal-windows">cd tensorflow</code>
</pre>

The repo defaults to the `master` development branch. You can also check out a
[release branch](https://github.com/tensorflow/tensorflow/releases)
to build:

<pre class="devsite-terminal tfo-terminal-windows prettyprint lang-bsh">
git checkout <em>branch_name</em>  # r1.9, r1.10, etc.
</pre>

Key Point: If you're having build problems on the latest development branch, try
a release branch that is known to work.

## Optional: Environmental Variable Set Up
Run the following commands before running the build command to avoid issues with package creation:
(If the below commands were set up while installing the packages, please ignore them). Run `set` to check if all the paths were set correctly, run `echo %Environmental Variable%` e.g., `echo %BAZEL_VC%` to check the path set up for a specific Environmental Variable

 Python path set up issue [tensorflow:issue#59943](https://github.com/tensorflow/tensorflow/issues/59943),[tensorflow:issue#9436](https://github.com/tensorflow/tensorflow/issues/9436),[tensorflow:issue#60083](https://github.com/tensorflow/tensorflow/issues/60083)

<pre class="devsite-terminal tfo-terminal-windows devsite-click-to-copy">
set PATH=path/to/python;%PATH% # [e.g. (C:/Python311)]
set PATH=path/to/python/Scripts;%PATH% # [e.g. (C:/Python311/Scripts)] 
set PYTHON_BIN_PATH=path/to/python_virtualenv/Scripts/python.exe 
set PYTHON_LIB_PATH=path/to/python virtualenv/lib/site-packages 
set PYTHON_DIRECTORY=path/to/python_virtualenv/Scripts 
</pre>

Bazel/MSVC/CLANG path set up issue [tensorflow:issue#54578](https://github.com/tensorflow/tensorflow/issues/54578)

<pre class="devsite-terminal tfo-terminal-windows devsite-click-to-copy">
set BAZEL_SH=C:/msys64/usr/bin/bash.exe 
set BAZEL_VS=C:/Program Files/Microsoft Visual Studio/2022/BuildTools 
set BAZEL_VC=C:/Program Files/Microsoft Visual Studio/2022/BuildTools/VC 
set Bazel_LLVM=C:/Program Files/LLVM (explicitly tell Bazel where LLVM is installed by BAZEL_LLVM, needed while using CLANG)
set PATH=C:/Program Files/LLVM/bin;%PATH% (Optional, needed while using CLANG as Compiler)
</pre>

## Optional: Configure the build

TensorFlow builds are configured by the `.bazelrc` file in the repository's
root directory. The `./configure` or `./configure.py` scripts can be used to
adjust common settings.

If you need to change the configuration, run the `./configure` script from
the repository's root directory.

<pre class="devsite-terminal tfo-terminal-windows devsite-click-to-copy">
python ./configure.py
</pre>

This script prompts you for the location of TensorFlow dependencies and asks for
additional build configuration options (compiler flags, for example). The
following shows a sample run of `python ./configure.py` (your session may
differ):

<section class="expandable">
<h4 class="showalways">View sample configuration session</h4>
<pre class="devsite-terminal tfo-terminal-windows">
python ./configure.py
You have bazel 6.5.0 installed.
Please specify the location of python. [Default is C:\Python311\python.exe]:

Found possible Python library paths:
C:\Python311\lib\site-packages
Please input the desired Python library path to use.  Default is [C:\Python311\lib\site-packages]

Do you wish to build TensorFlow with ROCm support? [y/N]:
No ROCm support will be enabled for TensorFlow.

WARNING: Cannot build with CUDA support on Windows.
Starting in TF 2.11, CUDA build is not supported for Windows. To use TensorFlow GPU on Windows, you will need to build/install TensorFlow in WSL2.

Do you want to use Clang to build TensorFlow? [Y/n]:
Add "--config=win_clang" to compile TensorFlow with CLANG.

Please specify the path to clang executable. [Default is C:\Program Files\LLVM\bin\clang.EXE]:

You have Clang 17.0.6 installed.

Please specify optimization flags to use during compilation when bazel option "--config=opt" is specified [Default is /arch:AVX]:

Would you like to override eigen strong inline for some C++ compilation to reduce the compilation time? [Y/n]:
Eigen strong inline overridden.

Would you like to interactively configure ./WORKSPACE for Android builds? [y/N]:
Not configuring the WORKSPACE for Android builds.

Preconfigured Bazel build configs. You can use any of the below by adding "--config=<>" to your build command. See .bazelrc for more details.
        --config=mkl            # Build with MKL support.
        --config=mkl_aarch64    # Build with oneDNN and Compute Library for the Arm Architecture (ACL).
        --config=monolithic     # Config for mostly static monolithic build.
        --config=numa           # Build with NUMA support.
        --config=dynamic_kernels        # (Experimental) Build kernels into separate shared objects.
        --config=v1             # Build with TensorFlow 1 API instead of TF 2 API.
Preconfigured Bazel build configs to DISABLE default on features:
        --config=nogcp          # Disable GCP support.
        --config=nonccl         # Disable NVIDIA NCCL support.
</pre>
</section>

## Build and install the pip package

The pip package is built in two steps. A `bazel build` command creates a
"package-builder" program. You then run the package-builder to create the
package.

### Build the package-builder

tensorflow:master repo has been updated to build 2.x by default.
[Install Bazel](https://docs.bazel.build/versions/master/install.html) and use
`bazel build ` to create the TensorFlow package-builder.

<pre class="devsite-terminal tfo-terminal-windows devsite-click-to-copy">
bazel build //tensorflow/tools/pip_package:wheel
</pre>

#### CPU-only

Use `bazel` to make the TensorFlow package builder with CPU-only support:

##### Build with MSVC 
<pre class="devsite-terminal tfo-terminal-windows devsite-click-to-copy">
bazel build --config=opt --repo_env=TF_PYTHON_VERSION=3.11 //tensorflow/tools/pip_package:wheel --repo_env=WHEEL_NAME=tensorflow_cpu
</pre>

##### Build with CLANG 
Use --config=`win_clang` to build TenorFlow with the CLANG Compiler:

<pre class="devsite-terminal tfo-terminal-windows devsite-click-to-copy">
bazel build --config=win_clang --repo_env=TF_PYTHON_VERSION=3.11 //tensorflow/tools/pip_package:wheel --repo_env=WHEEL_NAME=tensorflow_cpu
</pre>

#### GPU support

Note: GPU support on native-Windows is only available for 2.10 or earlier versions, starting in TF 2.11, CUDA build is not supported for Windows. For using TensorFlow GPU on Windows, you will need to build/install TensorFlow in WSL2 or use tensorflow-cpu with TensorFlow-DirectML-Plugin

To make the TensorFlow package builder with GPU support:

<pre class="devsite-terminal tfo-terminal-windows devsite-click-to-copy">
bazel build --config=opt --config=cuda --define=no_tensorflow_py_deps=true //tensorflow/tools/pip_package:build_pip_package
</pre>

Commands to clean the bazel cache to resolve errors due to invalid or outdated cached data, bazel clean with --expunge flag removes files permanently

<pre class="devsite-terminal tfo-terminal-windows devsite-click-to-copy">
bazel clean 
bazel clean --expunge  
</pre>

#### Bazel build options

Use this option when building to avoid issues with package creation:
[tensorflow:issue#22390](https://github.com/tensorflow/tensorflow/issues/22390)

<pre class="devsite-terminal tfo-terminal-windows devsite-click-to-copy">
--define=no_tensorflow_py_deps=true
</pre>

See the Bazel [command-line reference](https://docs.bazel.build/versions/master/command-line-reference.html)
for
[build options](https://docs.bazel.build/versions/master/command-line-reference.html#build-options).

Building TensorFlow from source can use a lot of RAM. If your system is
memory-constrained, limit Bazel's RAM usage with: `--local_ram_resources=2048`.

If building with GPU support, add `--copt=-nvcc_options=disable-warnings`
to suppress nvcc warning messages.

### Build the package

To build a pip package, you need to specify the --repo_env=WHEEL_NAME flag. 
Depending on the provided name, the package will be created. For example:

To build tensorflow CPU package:
<pre class="devsite-terminal devsite-click-to-copy">
bazel build //tensorflow/tools/pip_package:wheel --repo_env=WHEEL_NAME=tensorflow_cpu
</pre>

To build nightly package, set `tf_nightly` instead of `tensorflow`, e.g.
to build CPU nightly package:
<pre class="devsite-terminal devsite-click-to-copy">
bazel build //tensorflow/tools/pip_package:wheel --repo_env=WHEEL_NAME=tf_nightly_cpu
</pre>

As a result, generated wheel will be located in
<pre class="devsite-terminal devsite-click-to-copy">
bazel-bin/tensorflow/tools/pip_package/wheel_house/
</pre>


### Install the package

The filename of the generated `.whl` file depends on the TensorFlow version and
your platform. Use `pip install` to install the package, for example:

<pre class="devsite-terminal prettyprint lang-bsh">
pip install bazel-bin/tensorflow/tools/pip_package/wheel_house/tensorflow-<var>version</var>-<var>tags</var>.whl
</pre>

Success: TensorFlow is now installed.

## Build using the MSYS shell

TensorFlow can also be built using the MSYS shell. Make the changes listed
below, then follow the previous instructions for the Windows native command line
(`cmd.exe`).

### Disable MSYS path conversion {:.hide-from-toc}

MSYS automatically converts arguments that look like Unix paths to Windows
paths, and this doesn't work with `bazel`. (The label `//path/to:bin` is
considered a Unix absolute path since it starts with a slash.)

<pre class="prettyprint lang-bsh">
<code class="devsite-terminal">export MSYS_NO_PATHCONV=1</code>
<code class="devsite-terminal">export MSYS2_ARG_CONV_EXCL="*"</code>
</pre>

### Set your PATH {:.hide-from-toc}

Add the Bazel and Python installation directories to your `$PATH` environmental
variable. If Bazel is installed to `C:\tools\bazel.exe`, and Python to
`C:\Python\python.exe`, set your `PATH` with:

<pre class="prettyprint lang-bsh">
# Use Unix-style with ':' as separator
<code class="devsite-terminal">export PATH="/c/tools:$PATH"</code>
<code class="devsite-terminal">export PATH="/c/path/to/Python:$PATH"</code>
</pre>

For GPU support, add the CUDA and cuDNN bin directories to your `$PATH`:

<pre class="prettyprint lang-bsh">
<code class="devsite-terminal">export PATH="/c/Program Files/NVIDIA GPU Computing Toolkit/CUDA/v11.0/bin:$PATH"</code>
<code class="devsite-terminal">export PATH="/c/Program Files/NVIDIA GPU Computing Toolkit/CUDA/v11.0/extras/CUPTI/libx64:$PATH"</code>
<code class="devsite-terminal">export PATH="/c/tools/cuda/bin:$PATH"</code>
</pre>

Note: Starting in TF 2.11, CUDA build is not supported for Windows. For using TensorFlow GPU on Windows, you will need to build/install TensorFlow in WSL2 or use tensorflow-cpu with TensorFlow-DirectML-Plugin

<a name="tested_build_configurations"></a>
## Tested build configurations

### CPU

<table>
<tr><th>Version</th><th>Python version</th><th>Compiler</th><th>Build tools</th></tr>
<tr><td>tensorflow-2.19.0</td><td>3.9-3.12</td><td>CLANG 18.1.4</td><td>Bazel 6.5.0</td></tr>
<tr><td>tensorflow-2.18.0</td><td>3.9-3.12</td><td>CLANG 17.0.6</td><td>Bazel 6.5.0</td></tr>
<tr><td>tensorflow-2.17.0</td><td>3.9-3.12</td><td>CLANG 17.0.6</td><td>Bazel 6.5.0</td></tr>
<tr><td>tensorflow-2.16.1</td><td>3.9-3.12</td><td>CLANG 17.0.6</td><td>Bazel 6.5.0</td></tr>
<tr><td>tensorflow-2.15.0</td><td>3.9-3.11</td><td>MSVC 2019</td><td>Bazel 6.1.0</td></tr>
<tr><td>tensorflow-2.14.0</td><td>3.9-3.11</td><td>MSVC 2019</td><td>Bazel 6.1.0</td></tr>
<tr><td>tensorflow-2.12.0</td><td>3.8-3.11</td><td>MSVC 2019</td><td>Bazel 5.3.0</td></tr>
<tr><td>tensorflow-2.11.0</td><td>3.7-3.10</td><td>MSVC 2019</td><td>Bazel 5.3.0</td></tr>
<tr><td>tensorflow-2.10.0</td><td>3.7-3.10</td><td>MSVC 2019</td><td>Bazel 5.1.1</td></tr>
<tr><td>tensorflow-2.9.0</td><td>3.7-3.10</td><td>MSVC 2019</td><td>Bazel 5.0.0</td></tr>
<tr><td>tensorflow-2.8.0</td><td>3.7-3.10</td><td>MSVC 2019</td><td>Bazel 4.2.1</td></tr>
<tr><td>tensorflow-2.7.0</td><td>3.7-3.9</td><td>MSVC 2019</td><td>Bazel 3.7.2</td></tr>
<tr><td>tensorflow-2.6.0</td><td>3.6-3.9</td><td>MSVC 2019</td><td>Bazel 3.7.2</td></tr>
<tr><td>tensorflow-2.5.0</td><td>3.6-3.9</td><td>MSVC 2019</td><td>Bazel 3.7.2</td></tr>
<tr><td>tensorflow-2.4.0</td><td>3.6-3.8</td><td>MSVC 2019</td><td>Bazel 3.1.0</td></tr>
<tr><td>tensorflow-2.3.0</td><td>3.5-3.8</td><td>MSVC 2019</td><td>Bazel 3.1.0</td></tr>
<tr><td>tensorflow-2.2.0</td><td>3.5-3.8</td><td>MSVC 2019</td><td>Bazel 2.0.0</td></tr>
<tr><td>tensorflow-2.1.0</td><td>3.5-3.7</td><td>MSVC 2019</td><td>Bazel 0.27.1-0.29.1</td></tr>
<tr><td>tensorflow-2.0.0</td><td>3.5-3.7</td><td>MSVC 2017</td><td>Bazel 0.26.1</td></tr>
<tr><td>tensorflow-1.15.0</td><td>3.5-3.7</td><td>MSVC 2017</td><td>Bazel 0.26.1</td></tr>
<tr><td>tensorflow-1.14.0</td><td>3.5-3.7</td><td>MSVC 2017</td><td>Bazel 0.24.1-0.25.2</td></tr>
<tr><td>tensorflow-1.13.0</td><td>3.5-3.7</td><td>MSVC 2015 update 3</td><td>Bazel 0.19.0-0.21.0</td></tr>
<tr><td>tensorflow-1.12.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Bazel 0.15.0</td></tr>
<tr><td>tensorflow-1.11.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Bazel 0.15.0</td></tr>
<tr><td>tensorflow-1.10.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td></tr>
<tr><td>tensorflow-1.9.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td></tr>
<tr><td>tensorflow-1.8.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td></tr>
<tr><td>tensorflow-1.7.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td></tr>
<tr><td>tensorflow-1.6.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td></tr>
<tr><td>tensorflow-1.5.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td></tr>
<tr><td>tensorflow-1.4.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td></tr>
<tr><td>tensorflow-1.3.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td></tr>
<tr><td>tensorflow-1.2.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td></tr>
<tr><td>tensorflow-1.1.0</td><td>3.5</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td></tr>
<tr><td>tensorflow-1.0.0</td><td>3.5</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td></tr>
</table>

### GPU
Note: GPU support on native-Windows is only available for 2.10 or earlier versions, starting in TF 2.11, CUDA build is not supported for Windows. For using TensorFlow GPU on Windows, you will need to build/install TensorFlow in WSL2 or use tensorflow-cpu with TensorFlow-DirectML-Plugin

<table>
<tr><th>Version</th><th>Python version</th><th>Compiler</th><th>Build tools</th><th>cuDNN</th><th>CUDA</th></tr>
<tr><td>tensorflow_gpu-2.10.0</td><td>3.7-3.10</td><td>MSVC 2019</td><td>Bazel 5.1.1</td><td>8.1</td><td>11.2</td></tr>
<tr><td>tensorflow_gpu-2.9.0</td><td>3.7-3.10</td><td>MSVC 2019</td><td>Bazel 5.0.0</td><td>8.1</td><td>11.2</td></tr>
<tr><td>tensorflow_gpu-2.8.0</td><td>3.7-3.10</td><td>MSVC 2019</td><td>Bazel 4.2.1</td><td>8.1</td><td>11.2</td></tr>
<tr><td>tensorflow_gpu-2.7.0</td><td>3.7-3.9</td><td>MSVC 2019</td><td>Bazel 3.7.2</td><td>8.1</td><td>11.2</td></tr>
<tr><td>tensorflow_gpu-2.6.0</td><td>3.6-3.9</td><td>MSVC 2019</td><td>Bazel 3.7.2</td><td>8.1</td><td>11.2</td></tr>
<tr><td>tensorflow_gpu-2.5.0</td><td>3.6-3.9</td><td>MSVC 2019</td><td>Bazel 3.7.2</td><td>8.1</td><td>11.2</td></tr>
<tr><td>tensorflow_gpu-2.4.0</td><td>3.6-3.8</td><td>MSVC 2019</td><td>Bazel 3.1.0</td><td>8.0</td><td>11.0</td></tr>
<tr><td>tensorflow_gpu-2.3.0</td><td>3.5-3.8</td><td>MSVC 2019</td><td>Bazel 3.1.0</td><td>7.6</td><td>10.1</td></tr>
<tr><td>tensorflow_gpu-2.2.0</td><td>3.5-3.8</td><td>MSVC 2019</td><td>Bazel 2.0.0</td><td>7.6</td><td>10.1</td></tr>
<tr><td>tensorflow_gpu-2.1.0</td><td>3.5-3.7</td><td>MSVC 2019</td><td>Bazel 0.27.1-0.29.1</td><td>7.6</td><td>10.1</td></tr>
<tr><td>tensorflow_gpu-2.0.0</td><td>3.5-3.7</td><td>MSVC 2017</td><td>Bazel 0.26.1</td><td>7.4</td><td>10</td></tr>
<tr><td>tensorflow_gpu-1.15.0</td><td>3.5-3.7</td><td>MSVC 2017</td><td>Bazel 0.26.1</td><td>7.4</td><td>10</td></tr>
<tr><td>tensorflow_gpu-1.14.0</td><td>3.5-3.7</td><td>MSVC 2017</td><td>Bazel 0.24.1-0.25.2</td><td>7.4</td><td>10</td></tr>
<tr><td>tensorflow_gpu-1.13.0</td><td>3.5-3.7</td><td>MSVC 2015 update 3</td><td>Bazel 0.19.0-0.21.0</td><td>7.4</td><td>10</td></tr>
<tr><td>tensorflow_gpu-1.12.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Bazel 0.15.0</td><td>7.2</td><td>9.0</td></tr>
<tr><td>tensorflow_gpu-1.11.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Bazel 0.15.0</td><td>7</td><td>9</td></tr>
<tr><td>tensorflow_gpu-1.10.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td><td>7</td><td>9</td></tr>
<tr><td>tensorflow_gpu-1.9.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td><td>7</td><td>9</td></tr>
<tr><td>tensorflow_gpu-1.8.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td><td>7</td><td>9</td></tr>
<tr><td>tensorflow_gpu-1.7.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td><td>7</td><td>9</td></tr>
<tr><td>tensorflow_gpu-1.6.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td><td>7</td><td>9</td></tr>
<tr><td>tensorflow_gpu-1.5.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td><td>7</td><td>9</td></tr>
<tr><td>tensorflow_gpu-1.4.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td><td>6</td><td>8</td></tr>
<tr><td>tensorflow_gpu-1.3.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td><td>6</td><td>8</td></tr>
<tr><td>tensorflow_gpu-1.2.0</td><td>3.5-3.6</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td><td>5.1</td><td>8</td></tr>
<tr><td>tensorflow_gpu-1.1.0</td><td>3.5</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td><td>5.1</td><td>8</td></tr>
<tr><td>tensorflow_gpu-1.0.0</td><td>3.5</td><td>MSVC 2015 update 3</td><td>Cmake v3.6.3</td><td>5.1</td><td>8</td></tr>
</table>
