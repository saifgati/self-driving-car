# self-driving-car
Getting Started
Install
The first thing you need to do is to install all the libraries for the Raspberry Pi. To do so, open a terminal in Raspberry Pi and run:

$ cd raspi_utils/
$ bash install.sh
In the computer that you will perform the training -- protip: don't train the model in the Raspberry Pi! -- install all the requirements by runnig:

$ pip install -r requirements.txt
Usage
Attention: in the master branch all python code is written for Python 2. If you would like to run this project in Python 3, please switch to the python3 branch of this repository.

Collecting data
Before doing any kind of training you need to collect the track data. So in the Raspberry Pi -- with the assembled robot -- run the data collection script:

  $ cd self_driving/data_collection/ 
  $ python DataCollector.py -n <images_folder_name>
Pressing q will stop execution and save all images and pickle file.

Inside the folder <images_folder_name> there will be subdirectories organized by timestamps similar to 2018-02-17-23-27-02 with the collected *.png images. All the associated labels are saved in a pickle file 2018-02-17-23-27-02_pickle in <images_folder_name>.

Compress <images_folder_name> directory and export it from Raspberry Pi to other computer (using scp command, cloud, email, etc).

  $ tar cvf <images_folder_name>.tar <images_folder_name>
Attention: please continue following the instructions in the computer that will be use for training.

Generating npy and tfrecords
Before generating tfrecords, you need to transform the untar <images_folder_name> containing all folders of images and pickles into a tuple of np.arrays. Running the following script will result in the creation of <npy_files_name>_90_160_3_data.npy and <npy_files_name>_90_160_3_labels.npy files:

  $ cd self_driving/data_manipulation/
  $ python img2array.py <images_folder_path> <npy_folder_path> <npy_files_name>
To generate tfrecords from *.npy and augment or manipulate (e.g. binarize) the data, run:

 $ cd ../ml_training/ 
 $ python generate_tfrecords.py <npy_data_path> <npy_labels_path> -n <name_tfrecords> 
Resulting in <name_tfrecords>_train.tfrecords, <name_tfrecords>_test.tfrecords and <name_tfrecords>_valid.tfrecords files.

Hyperparameters optimization
Attention: all code in this section can be runned on both Python 2 and 3 with TensorFlow 1.2.1 (and above) and with GPU support, if possible.

Now it's time to test different architectures, learning rates and optimizers, in the hopes of improving accuracy.

Best architecture search
Running the following script will create architecture_results.txt file with the results for a given configuration passed through optional arguments.

 $ python best_architecture.py -n <name_tfrecords>
Best learning rate search
Running the following script will create learning_rate_results.txt file with the results for a given configuration passed through optional arguments.

 $ python best_learning_rate.py -n <name_tfrecords>
Best optimizer search
Running the following script will create optimizer_results.txt file with the results for a given configuration passed through optional arguments.

 $ python best_optimizer.py -n <name_tfrecords>
Training the model
Attention: back to Python 2

After searching for an appropriate combination of hyperparameters, you must train the model running this script with additional arguments relative to the model:

  $ python train.py -n <name_tfrecords> -v
The result will be a checkpoints directory with all files needed to deploy the model.

Accuracy test
You can test for accuracy with the script:

  $ python acc_test.py -n <name_tfrecords>
