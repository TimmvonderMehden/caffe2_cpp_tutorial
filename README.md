# Caffe2 C++ Tutorials and Examples

*C++ transcripts of the Caffe2 Python tutorials and other C++ example code.*


## About

Caffe2 has a strong C++ core but most tutorials only cover the outer Python layer of the framework. This project aims to provide example code written in C++, complementary to the Python documentation and tutorials. It covers verbatim transcriptions of most of the Python tutorials and other example applications.

Some higher level tools, like brewing models and adding gradient operations are currently not available in Caffe2's C++. This repo therefore provides some model helpers and other utilities as replacements.

Check out the actual Caffe2 Python tutorials at [https://caffe2.ai/docs/tutorials.html](https://caffe2.ai/docs/tutorials.html).


## Build

1. Install dependencies

    Install the dependencies CMake, leveldb and OpenCV. If you're on macOS, use Homebrew:

        brew install cmake leveldb opencv eigen

    On Ubuntu:

        apt-get install cmake libleveldb-dev libopencv-dev libopencv-core-dev libopencv-highgui-dev libeigen3-dev

    In case you're using CUDA an run into CMake issues with `NCCL`, try adding this to your `.bashrc` (assuming Caffe2 at `$HOME/caffe2`):

        export CMAKE_LIBRARY_PATH=$CMAKE_LIBRARY_PATH:$HOME/caffe2/third_party/nccl/build/lib

2. Install Caffe2

    Follow the Caffe2 installation instructions: [https://caffe2.ai/docs/getting-started.html](https://caffe2.ai/docs/getting-started.html)

3. Build using Make

    This project uses CMake. However easiest way to just build the whole thing is:

        make

    Internally it creates a `build` folder and runs CMake from there. This also downloads the resources that are required for running some of the tutorials.

Note: sources are developed and tested on macOS and Ubuntu.


## Intro Tutorial

The [Intro Tutorial](https://caffe2.ai/docs/intro-tutorial.html) covers the basic building blocks of Caffe2. This tutorial is transcribed in [intro.cpp](src/caffe2/binaries/intro.cpp).

Make sure to first run `make`. Then run the intro tutorial:

    ./bin/intro

This should output some numbers, including a `loss` of about `2.2`.

## Toy Regression

One of the most basic machine learning tasks is linear regression (LR). The [Toy Regression](https://caffe2.ai/docs/tutorial-toy-regression.html) tutorial shows how to get accurate results with a two-parameter model. This tutorial is transcribed in [toy.cpp](src/caffe2/binaries/toy)

Run the toy regression model:

	./bin/toy

This performs 100 steps of training, which should result in `W after` approximating `W ground truth`.

## Loading Pre-Trained Models

Often training can be skipped by using a pre-trained model. The [Model Zoo](https://github.com/caffe2/caffe2/wiki/Model-Zoo) contains a few of the popular models, although many are only available for Caffe. Use [caffe_translator.py](https://github.com/caffe2/caffe2/blob/master/caffe2/python/caffe_translator.py) to convert models to Caffe2. See [Caffe2 Models](https://github.com/leonardvandriel/caffe2_models) for more info.

The [Loading Pre-Trained Models](https://caffe2.ai/docs/tutorial-loading-pre-trained-models.html) tutorial shows how to use these models to classify images. This tutorial and more is covered in [pretrained.cpp](src/caffe2/binaries/pretrained.cpp). The code takes an input image and classifies its content. By default it uses the image in `res/image_file.jpg`. Make sure the pre-trained [Squeezenet](https://github.com/caffe2/models/tree/master/squeezenet) model is present in `res/squeezenet_*_net.pb`. To run:

    ./bin/pretrained

This should output something along the lines of `96% 'daisy'`. To classify `giraffe.jpg`:

    ./bin/pretrained --image_file giraffe.jpg
    
This tutorial is also a good test to see if OpenCV is working properly.

## MNIST - Create a CNN from Scratch

A classical machine learning dataset is the [MNIST database of handwritten digits](http://yann.lecun.com/exdb/mnist/) by Yann LeCun. The Caffe2 tutorial [MNIST - Create a CNN from Scratch](https://caffe2.ai/docs/tutorial-MNIST.html) shows how to build a basic convolutional neural network (CNN) to recognize these handwritten digits. This tutorial is transcribed in [mnist.cpp](src/caffe2/binaries/mnist.cpp). 

Make sure the databases folders `res/mnist-*-nchw-leveldb` are present. These should be generated by the  [download_resource.sh](script/download_resource.sh) script. Then run:

    ./bin/mnist

This performs 100 training runs, which should provide about 90% accuracy. The MNIST transcript is fairly verbose as it does not use any model helpers, like `NetUtil` and `ModelUtil`.

## RNNs and LSTM Networks

In [The Unreasonable Effectiveness of Recurrent Neural Networks](http://karpathy.github.io/2015/05/21/rnn-effectiveness/) Andrej Karpathy describes how to train a recurrent neural network (RNN) on a large volume of text and how to generate new text using such a network. The Caffe2 tutorial [RNNs and LSTM Networks](https://caffe2.ai/docs/RNNs-and-LSTM-networks.html) covers this technique using the [char_rnn.py](https://github.com/caffe2/caffe2/blob/master/caffe2/python/examples/char_rnn.py) script.

This tutorial is transcribed in [rnn.cpp](src/caffe2/binaries/rnn.cpp). It takes the same parameters as used in the tutorial. First make sure the file `res/shakespeare.txt` is present. Then run:

    ./bin/rnn
   
In contrast to the tutorial, this script terminates after 10K iterations. To get more, use `--train_runs`:

    ./bin/run --train_runs 100000

To get better results (loss < 1), expand the hidden layer:

    ./bin/rnn --train_runs 100000 --batch_size 32 --hidden_size 512 --seq_length 32

The file `res/dickens.txt` contains a larger volume of text. Because the writing is a bit more recent, it's more challenging to generate convincing results. Also, single newlines are stripped to allow for more creativity.

    ./bin/rnn --train_runs 100000 --batch_size 32 --hidden_size 768 --seq_length 32 --train_data res/dickens.txt
    
After 200K runs, the loss has not dropped below 36, in contrast to the shakespeare text. Perhaps this requires an additional hidden layer in the LSTM model.

## ImageNet

To classify images using pre-trained ImageNet models, use:

    ./bin/imagenet --model <name-of-model>

First download additional models:

	./script/download_extra.sh

All available models:

* `alexnet`: [AlexNet](https://github.com/BVLC/caffe/tree/master/models/bvlc_alexnet)
* `googlenet`: [GoogleNet](https://github.com/BVLC/caffe/tree/master/models/bvlc_googlenet)
* `squeezenet`: [SqueezeNet](https://github.com/DeepScale/SqueezeNet)
* `vgg16` and `vgg19`: [VGG Team](http://www.robots.ox.ac.uk/~vgg/research/very_deep/)
* `resnet50`, `resnet101`, `resnet152`: [MSRA](https://github.com/KaimingHe/deep-residual-networks)

These models are taken from the [Model Zoo](https://github.com/caffe2/caffe2/wiki/Model-Zoo) and [Caffe2 Models](https://github.com/leonardvandriel/caffe2_models).

## Retrain

The article [DeCAF: A Deep Convolutional Activation Feature for Generic Visual Recognition](https://arxiv.org/pdf/1310.1531v1.pdf) describes how to get good results on new datasets with minimal training efforts by reusing trained parameters of an existing model. For example, the above models are all trained on ImageNet data, which means they will only be able to classify ImageNet labels. However, by retraining just the top half of the model we can get high accuracy in a fraction of the time. If the image data has similar characteristics, it's possible to get good results by only retraining the top 'FC' layer.

First divide all images in subfolders with the label a folder name. Then to retrain the final layer of GoogleNet:

    ./bin/retrain --model googlenet --folder <image-folder> --layer pool5/7x7_s1
  
The script starts out by collecting all images and running them through the pre-trained part of the model. This allows for very fast training on the pre-processed image data.

If you have more (GPU) power at your disposal retrain VGG16's final 2 layers:

    ./bin/retrain --model vgg16 --folder <image-folder> --layer fc6

This

## Deep Dream

The article [Inceptionism: Going Deeper into Neural Networks](https://research.googleblog.com/2015/06/inceptionism-going-deeper-into-neural.html) describes how hidden layers of a CNN can be visualized by training just the the input tensor based on the mean value of a particular channel. This technique is known for producing remarkable imagery and can be combined with existing images. Ths is referred to as Deep Dream.

NB: this code is still a bit buggy and generated images are not representative of the original Deep Dream implementation.

The 139th channel in the `inception_4d/3x3` layer in GoogleNet:

    ./bin/dream --model googlenet --layer inception_4d/3x3_reduce --channel 139

Or if you have more (GPU) power at your disposal, the first channel in `conv3_1` layer in VGG16:

    ./bin/dream --model vgg16 --layer conv3_1

## Troubleshooting

See [http://rpg.ifi.uzh.ch/docs/glog.html](http://rpg.ifi.uzh.ch/docs/glog.html) for more info on logging. Try running the tools and examples with `--logtostderr=1`, `--caffe2_log_level=1`, and `--v=1`.
