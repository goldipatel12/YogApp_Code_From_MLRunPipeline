
## Getting Started
These instructions will show how to prepare your image data, train a model, and deploy the model to classify human action from image samples. See deployment for notes on how to deploy the project on a live stream.

### Prerequisites
- [tensorflow 2.0](https://www.tensorflow.org)
- [scikit-learn](https://scikit-learn.org/stable/)
- [opencv](https://opencv-python-tutroals.readthedocs.io/en/latest/)
- [pandas](https://pandas.pydata.org)
- [pillow](https://pillow.readthedocs.io/en/stable/)

### Installing
We recommend using a virtual environment to avoid any conflicts with your system's global configuration. You can install the required dependencies via pip:


### Example
After proprocessing your image data using the ```preprocess.py``` script, you can create a model by calling the ```actionModel()```function, which creates a scikit-learn pipeline. Then, call the ```trainModel()``` function with your data to train:

```python
# Stage your model
pipeline = actionModel(config.classifier())

# Train your model
model = trainModel(config.csv_path, pipeline)
```

## Data processing
Arrange your image data as a directory of subdirectories, each subdirectory named as a label for the images contained in it. Your directory structure should look like this:

```bash
├── images_dir
│   ├── class_1
│   │   ├── sample1.png
│   │   ├── sample2.jpg
│   │   ├── ...
│   ├── class_2
│   │   ├── sample1.png
│   │   ├── sample2.jpg
│   │   ├── ...
.   .
.   .
```
Samples should be standard image files recognized by the pillow library.

To generate a dataset from your images, run the ```preprocess.py``` script.
```bash
$ python preprocess.py
```
This will stage the labeled image dataset in a csv file written to the ```data/``` directory.

## Training
After reading the csv file into a dataframe, a custom scikit-learn transformer estimates body keypoints to produce a low-dimensional feature vector for each sample image. This representation is fed into a scikit-learn classifier set in the config file. This approach works well for lightweight applications that require classifying a pose.



Run the train.py script to train and save a classifier
```bash
$ python train.py
```

The pickled model will be saved in the ```models/``` directory


To train a more complex model to classify a sequence of poses culminating in an action (ie. squat or spin), use the ```train_sequential.py``` script. This script will train an LSTM model to classify movements.

```bash
$ python train_sequential.py
```


## Deployment
We've provided a sample inference script, ```inference.py```, that will read input from a webcam, mp4, or rstp stream, run inference on each frame, and print inference results.

If you are running on a Jetson Nano, you can try running the ```iva.py``` script, which will perform multi-person tracking and activity recognition like the demo gif above *Getting Started*. Make sure you have followed the Jetson Nano installation instructions above and simply run:
```bash
$ python iva.py 0

# or if you have a video file

$ python iva.py /path/to/file.mp4
```
If specified, this script will write a labeled video as ```out.mp4```. This demo uses a sample model called ```lstm_spin_squat.h5``` to classify spinning vs. squatting. Change the model and motion dictionary under the ```RUNSECONDARY``` flag to run your own classifier. 
