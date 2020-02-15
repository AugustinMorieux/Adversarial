Projet securité

Commande pip:


1. Install DeepSpeech
	- https://github.com/mozilla/DeepSpeech/blob/master/doc/TRAINING.rst
2. Install Audio Attack
3. Install Samantha



## Install Git Large File Storage 
You can install Git LFS using a package manager:
* To use Homebrew, run `brew install git-lfs`.
* To use MacPorts, run `port install git-lfs`.


## Getting the training code
Clone the Mozilla DeepSpeech repository into a folder called DeepSpeech:
```
git clone https://github.com/mozilla/DeepSpeech
```

## Creating a virtual environment
In creating a virtual environment you will create a directory containing a python3 binary and everything needed to run deepspeech. You can use whatever directory you want. For the purpose of the documentation, we will rely on $HOME/tmp/deepspeech-train-venv. You can create it using this command:

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
## Installing Python dependencies
Install the required dependencies using pip3:

cd DeepSpeech
pip3 install -r requirements.txt

The webrtcvad Python package might require you to ensure you have proper tooling to build Python modules:

sudo apt-get install python3-dev

You'll also need to install the ds_ctcdecoder Python package. ds_ctcdecoder is required for decoding the outputs of the deepspeech acoustic model into text. You can use util/taskcluster.py with the --decoder flag to get a URL to a binary of the decoder package appropriate for your platform and Python version:

pip3 install $(python3 util/taskcluster.py --decoder)

This command will download and install the ds_ctcdecoder package. You can override the platform with --arch if you want the package for ARM7 (--arch arm) or ARM64 (--arch arm64). If you prefer building the ds_ctcdecoder package from source, see the :github:`native_client README file <native_client/README.rst>`.

## Recommendations
If you have a capable (NVIDIA, at least 8GB of VRAM) GPU, it is highly recommended to install TensorFlow with GPU support. Training will be significantly faster than using the CPU. To enable GPU support, you can do:

pip3 uninstall tensorflow
pip3 install 'tensorflow-gpu==1.15.0'

Please ensure you have the required CUDA dependency.
It has been reported for some people failure at training:

tensorflow.python.framework.errors_impl.UnknownError: Failed to get convolution algorithm. This is probably because cuDNN failed to initialize, so try looking to see if a warning log message was printed above.
     [[{{node tower_0/conv1d/Conv2D}}]]

Setting the TF_FORCE_GPU_ALLOW_GROWTH environment variable to true seems to help in such cases. This could also be due to an incorrect version of libcudnn. Double check your versions with the TensorFlow 1.15 documentation.


## Getting the pre-trained model
If you want to use the pre-trained English model for performing speech-to-text, you can download it (along with other important inference material) from the DeepSpeech releases page. Alternatively, you can run the following command to download and unzip the model files in your current directory:

wget https://github.com/mozilla/DeepSpeech/releases/download/v0.4.1/deepspeech-0.4.1-checkpoint.tar.gz
tar -xzf deepspeech-0.4.1-checkpoint.tar.gz
