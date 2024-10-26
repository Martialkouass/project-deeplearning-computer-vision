# Skin Cancer Detection with 3D-TBP using a CNN (ISIC 2024 Contest)
**Contest link:** https://www.kaggle.com/competitions/isic-2024-challenge

# ► Introduction

This project takes color images of skin lesions and growths and applies a convolutional neural network (CNN) to determine if the lesion is malignant or benign. The images are accompanied by metadata containing the target value (1 = malignant, 0 = benign) and other information. The other information is integrated into the CNN after cleaning and selection of the most relevant features.

# ► The interest of the project

Skin cancer is a frequent form of cancer that can be deadly if not caught early. A model capable of determining if lesions are malignant would be useful in prefiltering patients before consultation with a specialist.

# ► How to Install and Run the Project

## Installation

### *Prerequisites:*
1.	Python 3.11.9 environment
2.	IDE with Jupyter Notebook extension
3.	Conda (for environment creation)
   
### *Files:*
***Supplied***
-	EDA.ipynb : Jupyter notebook containing the exploratory data analysis, data cleaning, feature selection, and preprocessing of metadata
-	Model.ipynb : Jupyter notebook containing the CNN model. Data augmentation is performed here.
-	hdf5_write.ipynb : code that generates a subset of metadata and images (optional)
-	requirements.txt : packages to be installed via pip in Conda

***On the Kaggle competition website (see link at top of readme)***  
*Note: Inscription required. The data has non distribution clauses.*
-	train-image.hdf5 : file containing all the images
-	train-metadata.csv : metadata file
Note: "train" here is specific to Kaggle. They have hidden validate and test datasets that our code does not use.

***Generated by EDA.ipynb***
-	cleaned-metadata.csv : the cleaned metadata file generated by EDA.ipynb and used by Model.ipynb

***Generated by Model.ipynb***
-	model_results_save.csv : Save of metrics calculated during all the epochs
-	test_ids.csv : Save of test_ids 
-	test_results_save.csv : Save of test target class, probability predictions, and predicted class
-	Model_Epoch_?.weights.h5 (? = Epoch number) : Save of model weights at the end of an epoch. The save frequency can be set.
-	Model_Last_Epoch.weights.h5 : Save of model weights after all Epochs.

***Generated by hdf5_write.ipynb***
- sample-image.hdf5
- sample-metadata.csv

## Making the code functional:
After completing the installation, the code will be functional after the following steps:
-	Place all ipynb files in the same directory
-	Place all csv and hdf5 files in a subdirectory to store input data ("/data" for example)
-	Create a new subdirectory for the save files ("/saves" for example)
#### EDA.ipynb:
-	At top of code in section titled "Create directory paths for the project", update the dataPath variable with the path for directory where the *train-metadata.csv* and *train-image.hdf5* files are stored.
-	In same section, adjust the file names if necessary
#### Model.ipynb:
-	In section 2.1, update the dataPath variable with the path for directory where the *cleaned-metadata.csv* and *train-image.hdf5* files are stored.
-	In same section, update the savePath variable with the path for directory where model outputs will be saved.
-	In same section, adjust the file names if necessary.
#### hdf5_write.ipynb (optional):
- At top of code, declare the filepaths and filenames.

## Details on project notebooks
### Using EDA.ipynb
The EDA file takes a couple hours to run in general.  
- Input files:
  - train-image.hdf5
  - sample-metadata.csv  
- Output files:
  - cleaned-metadata.csv

Basic setup is as follows:  
1.	At top of code in section titled "Create directory paths for the project", update the dataPath variable with the path for directory where the *train-metadata.csv* and *train-image.hdf5* files are stored.
2.	In same section, adjust the file names if necessary

### What the EDA does:
The EDA has multiple purposes. The unmodified metadata images are examined, including basic statistical parameters and the number of NAs. Some example images are printed to see what exactly the model will be dealing with. Different image augmentation algorithms are examined, with image printing to demonstrate them. Feature cleaning and normalization/standardization of performed. Then feature selection is performed, using 3 different models: Random Forest, Logistic Regression, and XGBoost. The final selected features are chosen manually, using the results from these models as a guide. Finally, the selected features are output into a file. This file contains isic_id, target, and cleaned features. It is the metadata input to Model.ipynb.

### Using Model.ipynb:
The Model file is highly parametrizeable. It can take many hours or several minutes, depending on user selections. This will be detailed later.  
- Input files:
  - train-image.hdf5
  - cleaned-metadata.csv
- Output files:
  -	model_results_save.csv : Save of metrics calculated during all the epochs
  -	test_ids.csv : Save of test_ids 
  -	test_results_save.csv : Save of test target class, probability predictions, and predicted class
  -	Model_Epoch_?.weights.h5 (? = Epoch number) : Save of model weights at the end of an epoch. The save frequency can be set.
  -	Model_Last_Epoch.weights.h5 : Save of model weights after all Epochs.

The Model has several main features: generation of train/validate/test lists, image augmentation, metadata dictionary creation (speeds up code), dataset creation (generator or loading in RAM), model definition, model fit, model test, and exportation of results/weights.
#### Generation of train/validate/test lists
The data is very unequilibrated, with very few target = 1 results. The following measures are used to ensure more equilibrated data:
1. Make two separate lists of isic_ids for target=0 and target=1. Transform into tuples (isic_id, target, mod toggle). Base data has mod toggle = 0, meaning no adjustment will be made.
2. Reserve 10% of target = 1 for validate
3. Split into train-validate & test on both lists (0 and 1). Take the test lists (0 and 1), concatenate and shuffle them
4. Create augmentation preparation function for target = 1: mod toggle = strictly positive integer (this adds more isic_ids to the list, with mod toggle non zero))
5. Run augmentation preparation function on train-validate and the reserved validation data: mod toggle = strictly positive integer
6. Split train-validate on both lists (0 and 1)
7. Reduce the validation data on target = 0 by value specified in reduce_frac
8. Concatenate and shuffle the train lists and validation lists
9. Limit training and validation data to speed up training (take only fraction of prepared lists)

## System requirements (minimum)<br>
Operating system: Windows, macOS, Linux<br>
GPU: not required, but much preferred (see Tensorflow for supported GPUs)<br>
CPU: 4-core, modern<br>
RAM: 8GB (16GB allows for accelerated processing)

## Server instances
The model has been tested on AWS SageMaker linked to an S3 bucket, with the input and output files stored in the S3 bucket. The preferred architecture for fast training is:<br>
***????????***
***????????***
***????????***
***????????***

# ► Credit
The primary project was created by:<br>
-	Claire Davat
-	Wilfried Cédric Koffi Djivo
-	Martial Kouassi
-	Andrew Wieber
