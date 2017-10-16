# About this environment

This environment includes **Pandas**, **Numpy** and other Open Source Machine Learning/Data Science libraries, to support the Python based machine learning courses and projects.

It started initially as a `Direnv` oriented environment, but I am in the process of evaluating `Conda` (via the `Anaconda` distribution) as I identified a couple of benefits in doing that.

As a quick "usage guide":

```
brew cask install anaconda 
conda env create -n new_env --file environment.yaml
conda env remove -n new
```

## eGPU

The environment is currently using an external GPU via a Node Akitio Thunderbolt 3 box and an Nvidia GTX 1080 Ti. Due to macOS versions and hardware, the whole setup runs on a MacBook Pro 2016 with Touch Bar and macOS 10.12.* (Sierra).

* The setup doesn't support hotplug. That means that the laptop is set in non-sleep mode (via Caffeine). Also, the eGPU gets plugged in and switched on before turning on the laptop. The laptop is switched off first.
* There was an issue with visual artifacts on screen. It seems disabling the *Automatic graphics switching* preferences in the *Energy Saver* system preferences section resolves this by sticking to the best card.

To access the system from other computers (and benefit from more screen real estate) the Jupyter configuration allows access from other systems in the network. To expose this outside, the recommendation is using `ngrok` (access speed is penalized due to tunneling though).

The set up can also work without the eGPU connected, by setting `CUDA_VISIBLE_DEVICES=''` or `'-1'`, but this behaviour is not consistent and running the neural networks can fail sometimes due to issues loading the driver. This is quite non-deterministic and there is a better option, consisting on using an installation of TensorFlow without GPU support. Note as well that support for TensorFlow with GPU on macOS ceased to be provided on version 1.2.

## Courses and projects

Course notebooks, code and documentation are to be tracked in their own repository. To simplify reusability (and because of the way `Direnv` works, which doesn't sit well with Dropbox and sharing the environment across multiple computers with different versions of macOS), the environment is not shared (it is synchronized via GIT), while the course can be if so wanted.

The course and project folders are linked as `course`, `kaggle_quora`, etc... This is a recommendation, obviously, and you can choose to integrate this environment and your projects as you wish.

## Direnv

When using `direnv`, the virtualenv environment is setup and activated upon accessing the directory.

`pip install -r requirements.txt`

If the containing folder gets moved, it is better to throw away (delete) the `.direnv` folder as some paths are configured statically during the automated setup.

In some cases, specific libraries my require adhoc installation, please review the notes below.

## Notes about Libraries and Tools

Please note that this documentation uses GCC 6, but you could equally use GCC 7, which might be more compatible with your setup.

### TensorFlow / TensorBoard

TensorFlow installation on macOS requires `pip install --upgrade https://storage.googleapis.com/tensorflow/mac/gpu/tensorflow_gpu-1.1.0-py3-none-any.whl` if supporting GPU. Official support for GPUs on macOS has been dropped in the latest versions, which is why my installation sticks to `1.1.0`.

TensorBoard might require installation via `pip install tensorflow-tensorbard==0.1.2` due to some issues with the regex filters for runs. More recent versions require TensorFlow `1.3.x`, which is not supported on macOS GPU (and therefore not practical with my setup).

### XGBoost

The `pip` version of XGBoost is using an old compiler for macOS, and it might be prefereable running a manual installation directly from the [github repo](https://github.com/dmlc/xgboost).

On macOS, where a gcc version with OpenMP support should be used to benefit from the multi-threading.

```
brew install gcc --without-multilib
git clone --recursive https://github.com/dmlc/xgboost
export CC=$(which gcc-6)
export CXX=$(which g++-6)
cd xgboost; cp make/config.mk ./config.mk
make clean && make -j4
cd python-package
# The next step might NOT be what you want,
# particularly if you need control on the destination path
python setup.py install
```

In some cases (like when using `direnv`), there will be two constraints:

* which version of Python to use
* the `setup.py` scripts needs to be executed from its own directory

One way of solving this is from the virtualenv environment itself is running:

```
brew install gcc --without-multilib
git clone --recursive https://github.com/dmlc/xgboost /tmp/xgboost
export CC=$(which gcc-6)
export CXX=$(which g++-6)
cd /tmp/xgboost; cp make/config.mk ./config.mk
make clean && make -j4
cd python-package
```
, and (from the virtualenv environment),

```
python -c "import subprocess; subprocess.Popen(['python','setup.py', 'install'], cwd='/tmp/xgboost/python-package')"
```

, where `/tmp/xgboost/python-package` should be replaced by the directory containing `setup.py`. After finishing the installation, you should press `[Enter]`.

### spaCy

The `pip` version of spaCy is not optimized for multithreading in macOS. Therefore it is prefereable running a manual installation directly from the [github repo](https://github.com/explosion/spaCy).

On macOS, where a gcc version with OpenMP support should be used to benefit from the multi-threading.

```
brew install gcc --without-multilib
git clone --recursive https://github.com/explosion/spaCy
export CC=$(which gcc-6)
export CXX=$(which g++-6)
# The next step might NOT be what you want,
# particularly if you need control on the destination path
python setup.py install
```

In some cases (like when using `direnv`), there will be two constraints:

* which version of Python to use
* the `setup.py` scripts needs to be executed from its own directory

One way of solving this is from the virtualenv environment itself is running:

```
brew install gcc --without-multilib
git clone --recursive https://github.com/explosion/spaCy /tmp/spaCy
export CC=$(which gcc-6)
export CXX=$(which g++-6)
```
, and (from the virtualenv environment),

```
python -c "import subprocess; subprocess.Popen(['python','setup.py', 'install'], cwd='/tmp/spaCy')"
```

, where `/tmp/spaCy` should be replaced by the directory containing `setup.py`. After finishing the installation, you should press `[Enter]`.



### Numpy

#### OpenBLAS (macOS)

In macOS, the default numpy installation is linked to vecLib - Accelerate. It might be worth investigating OpenBLAS as a more performant solution (or more performant solution in some cases).

To do so, you can install it as below (it will make sure it is reinstalled from the source, regardless of which versions to might have already in your environment): 

```
brew install openblas
export OPENBLAS=$(brew --prefix openblas)/lib
pip install numpy -U --force-reinstall --no-cache-dir --no-binary :all:
```

You can check the installation via:

`python -c "import numpy; print(numpy.show_config())"`

Tests on my iMac with macOS 10.9.5 don't show any significant improvement.


### Matplotlib

#### To set a matplotlib backend for non-GUI python shells

This would be needed, for instance, on macOS.

```
echo "backend: TkAgg" >> ~/.matplotlib/matplotlibrc
```

### PyAudio

This is a wrapper library for PortAudio, used when `SpeechRecognition` acesses the microphone.

```
brew install portaudio
```

### PocketSphinx

It needs `swig`, which might not be installed on your system.

```
brew install swig
```

### Jupyter

#### Extensions

After installing the contributed notebook extensions (from `jupyter_contrib_nbextensions`), they need to be copied to the system:

`jupyter contrib nbextension install --sys-prefix`

With this they will be available via the internal extension configuration tab. Or using the command line:

```
jupyter nbextension enable hide_header/main
jupyter nbextension enable freeze/main
jupyter nbextension execute_time/ExecuteTime
```

#### To find out which Python kernels are available in `Jupyter`

`jupyter kernelspec list`

#### To execute `Jupyter` notebooks non-interactively (and generated documents)

* To execute: `--ExecutePreprocessor.enabled=True`
, or just `--execute`.
* To strip outputs: `--ClearOutputPreprocessor.enabled=True`
* To allow errors: `--allow-errors`
* To generate a PDF: `--to=pdf`. This requires `pandoc` and `mactex` (for instance). Also, the `pdftex` or `xelatex` binaries need to be in the `PATH`. For mactex installed via Homebrew that's `/Library/TeX/texbin`.

An example of the excution would be:

```
jupyter nbconvert --to=pdf --execute <notebook>
```

#### Use from other computers

You might have a central server you want to use for your runs, while using a different computer to interface with that server.

One way of doing this is creating a configuration file which allows for such a setting. The simplest way is:

* Create an empty file `.jupyter/jupyter_notebook_config.py`.
* Add the following content into that file:

  ```
  c.NotebookApp.ip = '*'  # serve the notebooks locally
c.NotebookApp.open_browser = False  # do not open a browser window by default when using notebooks
c.NotebookApp.password = 'sha1:b02b20954610:599cae086531cbc8e268c88348bc4e6913779103'
  ```

**Note :** The password comes from the execution of `python -c 'from IPython.lib import passwd; print(passwd())'`.

## Issues

### Possible issue with `python3`

When using a `python3` installed via `brew install` it might happen that there are already `/usr/local/bin/python3` links to previous installations (which doesn't work as the `python3` from Homebrew requires a System or a Homebrew set of binaries, and installations under `/Library` might fail). If you have issues with this, execute `brew doctor`.

A way in which this issue might present itself is through this type of error messages:

```
Fatal Python error: Py_Initialize: unable to load the file system codec
LookupError: no codec search functions registered: can't find encoding

Current thread 0x0000000100f85310 (most recent call first):
```