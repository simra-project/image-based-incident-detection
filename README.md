# README

This repository contains Jupyter notebooks to create Gramian Angular Field (GAF) images from SimRa timeseries data.
The intution behind this is that such images can be used to use the image recognition capabilites of neural networks to detect, for example, incidents in given ride segments (also see Literature below).

## Jupyter Notebooks

Each notebook contains install commands for the necessary dependencies in the first cell.
In total, there are three notebooks.

### gaf-playground

This notebook contains code to explore how GAF images are created based on timeseries data.

### incident-acc-image-generation.ipynb

This notebook contains code to create GAF images based on the dataset defined in the third cell.
Running from top to bottom, the notebook contains the code to do the following tasks **for a single ride**:

- create a pandas data frame for (1) the ride (2) the incidents
- check if the ride is valid, i.e., contains at least one incident that can be mapped to the ride
- calculate to which datapoints in the ride dataframe each incident is mapped based on the timestamp
- create sliding windows (length == 10) for the ride and add labels to each window (either has incident or does not have incident)
- create GAF images for each window with an incident, and GAF images for as many randomly selected windows without incidents
- store the incidents, if the filename contains *true*, the corresponding window contains an incident

The last cell of the notebook contains code to do the tasks above for each ride.
Rides that are not valid are discarded during image generation.
The images that have been created for the commited notebook output (rides from Berlin between Mai and July) can be found at `images/Berlin/rides/2020`.

### simra_fastai.ipynb

This notebook contains code to train and validate a pre-trained resnet34 neural network.
This notebook uses [fastai](https://docs.fast.ai/), setting up an environment can be complicated, so you should definetly use [Google Colab](https://course.fast.ai/start_colab).
The images dataset is small enough to be uploaded to Google Drive, which can be accessed directly from a Colab notebook.

The notebook reads in the images data, trains the network, and then prints out some information on the success.
If you would like to understand better what is going on, I recommend reading the first chapter of the [fastai book](https://github.com/fastai/fastbook/blob/master/01_intro.ipynb).
There is also a [video course](https://course.fast.ai/).

My result interpretation:
The accuracy (1 - error_rate) is way to low for use in practice, i.e., about **60% per window**.
There are, however, straight forward improvement options:

- Use all accelerometer data (10 Hz) and not only rows with valid GPS data (1/3 Hz)
- Also use manually tagged incidents; they are harder to use since they do not have correct timestamp information
- Do not consider all kinds of incidents together
- Add more data dimensions, i.e., multivariate time series data (see literature below)
- Ensure that incidents are not to close to the start/end of each window

Other things to experiment with:

- Do not use sliding windows
- Experiment with longer/shorter windows
- Use another approach for aggregating X, Y, Z accelerometer values into a single value, or not aggregate them at all

## Literature

[Blog Post (towardsdatascience): time series to image for financial forecasting](https://towardsdatascience.com/how-to-encode-time-series-into-images-for-financial-forecasting-using-convolutional-neural-networks-5683eb5c53d9)

- High level explanation, builds on two papers:
  - How time series can be encoded in images
  - How the process can be used for financial forecasting
- Code examples for the complete process

[Paper: encoding multivariate time series](https://www.researchgate.net/publication/338205889_Sensor_Classification_Using_Convolutional_Neural_Network_by_Encoding_Multivariate_Time_Series_as_Two-Dimensional_Colored_Images/fulltext/5e06c478299bf10bc37e2f26/Sensor-Classification-Using-Convolutional-Neural-Network-by-Encoding-Multivariate-Time-Series-as-Two-Dimensional-Colored-Images.pdf?origin=publication_detail)

- Extends the concept to multivariate time series data
- Also contains a good explanation of the univariate process