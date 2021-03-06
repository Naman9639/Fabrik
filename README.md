<img width="25%" src="/ide/static/img/logo.png" />

[![Join the chat at https://gitter.im/Cloud-CV/Fabrik](https://badges.gitter.im/Cloud-CV/Fabrik.svg)](https://gitter.im/Cloud-CV/Fabrik?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![Build Status](https://travis-ci.org/Cloud-CV/Fabrik.svg?branch=master)](https://travis-ci.org/Cloud-CV/Fabrik)
[![Coverage Status](https://coveralls.io/repos/github/Cloud-CV/Fabrik/badge.svg?branch=master)](https://coveralls.io/github/Cloud-CV/Fabrik?branch=master)

Fabrik is an online collaborative platform to build, visualize and train deep learning models via a simple drag-and-drop interface. It allows researchers to collaboratively develop and debug models using a web GUI that supports importing, editing and exporting networks written in widely popular frameworks like Caffe, Keras, and TensorFlow.

<img src="/example/fabrik_demo.gif?raw=true">

This app is presently under active development and we welcome contributions. Please check out our [issues thread](https://github.com/Cloud-CV/IDE/issues) to find things to work on, or ping us on [Gitter](https://gitter.im/Cloud-CV/IDE).


## Installation Instructions

Setting up Fabrik on your local machine is really easy. You can setup Fabrik using two methods:

### Using Docker

1. Get the source code on to your machine via git.

    ```shell
    git clone https://github.com/Cloud-CV/Fabrik.git && cd Fabrik
    ```

2. Rename `settings/dev.sample.py` as `dev.py`.

    ```
    cp settings/dev.sample.py settings/dev.py
    ```

3. Build and run the Docker containers. This might take a while. You should be able to access Fabrik at `0.0.0.0:8000`.

    ```
    docker-compose up --build
    ```

### Using Virtual Environment
1. First set up a virtualenv
    ```
    sudo apt-get install python-pip python-dev python-virtualenv
    virtualenv --system-site-packages ~/Fabrik
    source ~/Fabrik/bin/activate
    ```

2. Clone the repository
    ```
    git clone --recursive https://github.com/Cloud-CV/Fabrik.git
    ```

3. Rename settings/dev.sample.py as settings/dev.py and change credential in settings/dev.py
    ```
    cp settings/dev.sample.py settings/dev.py
    ```

4. Install redis server and replace the hostname to 'localhost' in settings/common.py line 99.
    ```
    sudo apt-get install redis-server
    ```

5. If you have Caffe, Keras and Tensorflow already installed on your computer, skip this step
    * For Linux users
        ```
        cd Fabrik/requirements
        yes Y | sh caffe_tensorflow_keras_install.sh
        ```
        Open your ~/.bashrc file and append this line at the end
        ```        
        export PYTHONPATH=~/caffe/caffe/python:$PYTHONPATH
        ```
        Save, exit and then run
        ```
        source ~/.bash_profile
        cd ..
        ```
    * For Mac users
        * [Install Caffe](http://caffe.berkeleyvision.org/install_osx.html)
        * [Install Tensorflow](https://www.tensorflow.org/install/install_mac)
        * [Install Keras](https://keras.io/#installation)
6. Install dependencies
* For developers:
    ```
    pip install -r requirements/dev.txt
    ```
* Others:
    ```
    pip install -r requirements/common.txt
    ```
7. [Install postgres >= 9.5](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04)
* Setup postgres database
    * Start postgresql by typing ```sudo service postgresql start```
    * Now login as user postgres by running ```sudo -u postgres psql``` and type the commands below

    ```
      CREATE DATABASE fabrik;
      CREATE USER admin WITH PASSWORD 'fabrik';
      ALTER ROLE admin SET client_encoding TO 'utf8';
      ALTER ROLE admin SET default_transaction_isolation TO 'read committed';
      ALTER ROLE admin SET timezone TO 'UTC';
      ALTER USER admin CREATEDB;
    ```
    * Exit psql by typing in \q and hitting enter.
* Migrate
    ```

    python manage.py makemigrations caffe_app
    python manage.py migrate
    ```
8. Install node modules
```
npm install
npm install --save-dev json-loader
sudo npm install -g webpack
webpack --progress --watch --colors
```

### Usage
```
python manage.py runserver
```

### Example
* Use `example/tensorflow/GoogleNet.pbtxt` for tensorflow import
* Use `example/caffe/GoogleNet.prototxt` for caffe import
* Use `example/keras/vgg16.json` for keras import

### Tested models

The model conversion between currently supported frameworks is tested on some models.

Models                                                                      | Caffe | Keras | Tensorflow |
:--------------------------------------------------------------------------:|:-----:|:-----:|:----------:|
[Inception V3](http://arxiv.org/abs/1512.00567)                             |   √   |   √   |     √      |
[Inception V4](http://arxiv.org/abs/1512.00567)                             |   √   |   √   |     √      |
[ResNet 101](https://arxiv.org/abs/1512.03385)                              |   √   |   √   |     √      |
[VGG 16](http://arxiv.org/abs/1409.1556.pdf)                                |   √   |   √   |     √      |
[GoogLeNet](https://arxiv.org/pdf/1610.02357.pdf)                           |   √   |   ×   |     ×      |
[SqueezeNet](https://arxiv.org/pdf/1602.07360)                              |   √   |   ×   |     ×      |
[DenseNet](https://arxiv.org/abs/1608.06993)                                |   √   |   ×   |     ×      |
[AllCNN](https://arxiv.org/abs/1412.6806)                                   |   √   |   ×   |     ×      |
[AlexNet](http://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks)                                 |   √   |   √   |     √      |
[FCN32 Pascal](https://www.cv-foundation.org/openaccess/content_cvpr_2015/html/Long_Fully_Convolutional_Networks_2015_CVPR_paper.html) |   √   |   ×   |     ×      |
[YoloNet](https://arxiv.org/abs/1506.02640)                                 |   √   |   √   |     √      |
[Pix2Pix](https://github.com/phillipi/pix2pix)                              |   √   |   ×   |     ×      |
[VQA](https://github.com/iamaaditya/VQA_Demo)                               |   √   |   √   |     √      |

Note: For models that use a custom LRN layer (Alexnet), Keras expects the custom layer to be passed when it is loaded from json. LRN.py is located in keras_app/custom_layers. [Alexnet import for Keras](https://github.com/Cloud-CV/Fabrik/blob/master/tutorials/keras_custom_layer_usage.md)

### Documentation
* [Using a Keras model exported from Fabrik](https://github.com/Cloud-CV/Fabrik/blob/master/tutorials/keras_json_usage_1.md)
* [Loading a Keras model exported from Fabrik and printing its summary](https://github.com/Cloud-CV/Fabrik/blob/master/tutorials/keras_json_usage_2.md)
* [Using an Exported Caffe Model](https://github.com/Cloud-CV/Fabrik/blob/master/tutorials/caffe_prototxt_usage_1.md)
* [Loading a caffe model in python and printing its parameters and output size](https://github.com/Cloud-CV/Fabrik/blob/master/tutorials/caffe_prototxt_usage_2.md)
* [List of models tested with Fabrik](https://github.com/Cloud-CV/Fabrik/blob/master/tutorials/tested_models.md)
* [Adding model to the Fabrik model zoo](https://github.com/Cloud-CV/Fabrik/blob/master/tutorials/adding_new_model.md)
* [Adding new layers](https://github.com/Cloud-CV/Fabrik/blob/master/tutorials/adding_new_layers.md)
* [Using custom layers with Keras](https://github.com/Cloud-CV/Fabrik/blob/master/tutorials/keras_custom_layer_usage.md)
* [Linux installation walk-through](https://www.youtube.com/watch?v=zPgoben9D1w)

### License

This software is licensed under GNU GPLv3. Please see the included License file. All external libraries, if modified, will be mentioned below explicitly.
