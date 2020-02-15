# Projet securité
---

1. Install DeepSpeech
	- https://github.com/mozilla/DeepSpeech/blob/master/doc/TRAINING.rst
2. Install Audio Attack
	- https://github.com/carlini/audio_adversarial_examples
3. Install Samantha


## Install Git Large File Storage 
You can install Git LFS using a package manager:
* To use Homebrew, run `brew install git-lfs`.
* To use MacPorts, run `port install git-lfs`.

## Installing Pipenv 
<https://docs.python-guide.org/dev/virtualenvs/>
Pipenv is a dependency manager for Python projects. If you’re familiar with Node.js’ npm or Ruby’s bundler, it is similar in spirit to those tools. While pip can install Python packages, Pipenv is recommended as it’s a higher-level tool that simplifies dependency management for common use cases.
Use pip to install Pipenv:
```
pip install --user pipenv
```

## Creating a virtual environment
In creating a virtual environment you will create a directory containing a python3 binary and everything needed to run deepspeech. You can use whatever directory you want. For the purpose of the documentation, we will rely on `$HOME/tmp/deepspeech-train-venv`. You can create it using this command:

```
pip install virtualenv
sudo /usr/bin/easy_install virtualenv
```
then
```
virtualenv -p python3 $HOME/tmp/deepspeech-train-venv/
```

## Activating the environment
Each time you need to work with DeepSpeech, you have to activate this virtual environment. This is done with this simple command:
``` 
source $HOME/tmp/deepspeech-train-venv/bin/activate
```
## Installing DeepSpeech Python bindings

Once your environment has been set-up and loaded, you can use pip3 to manage packages locally. On a fresh setup of the virtualenv, you will have to install the DeepSpeech wheel. You can check if deepspeech is already installed with pip3 list.

To perform the installation, just use pip3 as such:
```
pip3 install deepspeech
```
If deepspeech is already installed, you can update it as such:
```
pip3 install --upgrade deepspeech
```
Alternatively, if you have a supported NVIDIA GPU on Linux, you can install the GPU specific package as follows:
```
pip3 install deepspeech-gpu
```
See the release notes to find which GPUs are supported. Please ensure you have the required CUDA dependency.
You can update deepspeech-gpu as follows:
```
pip3 install --upgrade deepspeech-gpu
```
In both cases, pip3 should take care of installing all the required dependencies. After installation has finished, you should be able to call deepspeech from the command-line.

## Getting the training code
Clone the Mozilla DeepSpeech repository into a folder called DeepSpeech:
```
git clone https://github.com/mozilla/DeepSpeech
```
Checkout the correct version of the code:
```
cd DeepSpeech
git checkout tags/v0.4.1
```
> If you get an error with tflite_convert, comment out DeepSpeech.py Line 21
`# from tensorflow.contrib.lite.python import tflite_convert`
## Installing Python dependencies
Install the required dependencies using pip3:
```
cd DeepSpeech
pip3 install -r requirements.txt
```
The webrtcvad Python package might require you to ensure you have proper tooling to build Python modules:
```
sudo apt-get install python3-dev
```
You'll also need to install the ds_ctcdecoder Python package. ds_ctcdecoder is required for decoding the outputs of the deepspeech acoustic model into text. You can use util/taskcluster.py with the --decoder flag to get a URL to a binary of the decoder package appropriate for your platform and Python version:
```
pip3 install $(python3 util/taskcluster.py --decoder)
```
> if it is not working : `python3 util/taskcluster.py --arch osx --target` 

This command will download and install the ds_ctcdecoder package. You can override the platform with --arch if you want the package for ARM7 (--arch arm) or ARM64 (--arch arm64). If you prefer building the ds_ctcdecoder package from source, see the :github:`native_client README file <native_client/README.rst>`.

## Recommendations
If you have a capable (NVIDIA, at least 8GB of VRAM) GPU, it is highly recommended to install TensorFlow with GPU support. Training will be significantly faster than using the CPU. To enable GPU support, you can do:
```
pip3 uninstall tensorflow
pip3 install 'tensorflow-gpu==1.15.0'
```
Please ensure you have the required CUDA dependency.
It has been reported for some people failure at training:
```
tensorflow.python.framework.errors_impl.UnknownError: Failed to get convolution algorithm. This is probably because cuDNN failed to initialize, so try looking to see if a warning log message was printed above.
     [[{{node tower_0/conv1d/Conv2D}}]]
```
Setting the TF_FORCE_GPU_ALLOW_GROWTH environment variable to true seems to help in such cases. This could also be due to an incorrect version of libcudnn. Double check your versions with the TensorFlow 1.15 documentation.

## Getting pre-trained models
If you want to use the pre-trained English model for performing speech-to-text, you can download it (along with other important inference material) from the DeepSpeech releases page. Alternatively, you can run the following command to download and unzip the model files in your current directory:
```
wget https://github.com/mozilla/DeepSpeech/releases/download/v0.4.1/deepspeech-0.4.1-checkpoint.tar.gz
tar -xzf deepspeech-0.4.1-checkpoint.tar.gz
```

```
wget https://github.com/mozilla/DeepSpeech/releases/download/v0.6.1/deepspeech-0.6.1-models.tar.gz
tar xvfz deepspeech-0.6.1-models.tar.gz
```

## Getting the audio adversarial Attack
```
git clone https://github.com/carlini/audio_adversarial_examples.git
```

## Download example audio files
```
curl -LO https://github.com/mozilla/DeepSpeech/releases/download/v0.6.1/audio-0.6.1.tar.gz
tar xvf audio-0.6.1.tar.gz
```

## Transcribe an audio file
```
deepspeech --model deepspeech-0.6.1-models/output_graph.pbmm --scorer deepspeech-0.6.1-models/kenlm.scorer --audio audio/2830-3980-0043.wav
```
The `--scorer` argument is optional, and represents an external language model to be used when transcribing the audio.
## Check that you can classify normal audio files correctly 
```
python3 classify.py --in sample-000000.wav --restore_path deepspeech-0.4.1-checkpoint/model.v0.4.1
```

## Generate adversarial examples 
```
python3 attack.py --in sample-000000.wav --target "this is a test" --out adv.wav --iterations 1000 --restore_path deepspeech-0.4.1-checkpoint/model.v0.4.1
```

## Verify the attack succeeded 
```
python3 classify.py --in adv.wav --restore_path deepspeech-0.4.1-checkpoint/model.v0.4.1
```


## Install Microphone VAD Streaming
Stream from microphone to DeepSpeech, using VAD (voice activity detection). A fairly simple example demonstrating the DeepSpeech streaming API in Python. Also useful for quick, real-time testing of models and decoding parameters.


```
sudo apt install portaudio19-dev python3-dev
```

Installation on MacOS may fail due to portaudio, use brew to install it:

```
brew install portaudio
```

```
pip install -r requirements.txt
```

Uses portaudio for microphone access, so on Linux, you may need to install its header files to compile the pyaudio package:

## Additionnal problems:

with numpy, try:
```
pip install --upgrade --ignore-installed --install-option '--install-data=/usr/local' numpy
```
or
```
sudo easy_install numpy
```

with tensirflow, try:
```
pip install tensorflow
```

with progressbar, try:
```
pip install progressbar2
```




