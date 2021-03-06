# Flower17Implementation

In this repository, 3 different model is trained with flower17 dataset, and by comparing their relative performance, I try to explain two important concept of machine learning called data augmentation and transfer learning. How this two technique facilitates the classification accuracy is also explained.

### MiniVGGNet

For the first model, I have considered the MiniVGG network. We will provide 64 * 64 * 3 input of this model. Here is the model structure in tabular format. 

| Layer Type | Output Size | Filter Size / Stride |
| --- | --- | --- |
| Input image |  64 × 64 × 3 |    |
| CONV | 32 × 32 × 32 | 3 × 3, K = 32 |
| ACT | 32 × 32 × 32 |  |
| BN | 32 × 32 × 32 |  |
| CONV | 32 × 32 × 32 | 3 × 3, K = 32 |
| ACT | 32 × 32 × 32 |  |
| BN | 32 × 32 × 32 |  |
| POOL | 16 × 16 × 32 | 2 × 2 |
| DROPOUT | 16 × 16 × 32 |  |
| CONV | 16 × 16 × 64 | 3 × 3, K = 64 |
| ACT | 16 × 16 × 64 | |
| BN | 16 × 16 × 64 | |
| CONV | 16 × 16 × 64 | 3 × 3, K = 64 |
| ACT | 16 × 16 × 64 | |
| BN | 16 × 16 × 64 | |
| POOL | 8 × 8 × 64 | 2 × 2 |
| DROPOUT | 8 × 8 × 64 | |
| FC | 512 | |
| ACT | 512 | |
| BN | 512 | |
| DROPOUT | 512 | |
| FC | 17 | |
| SOFTMAX | 17 | |

To run this model:

`python minivggnet.py --dataset <path to dataset>`

However, the flower17 dataset is not included in this repository due to its size. Please **download** the dataset to the root of the directory structure before run the code.

If we look at the classification report, the accuracy is in 50% range, which is not satisfactory. 

![alt text](minivgg_report.png)

Besides, Here is the training loss and accuracy graph for training and test data.

![alt text](minivgg.png)

This report clearly shows signs of overfitting; i.e the model can't generalize well. 


In that case, we can apply some regularization technique. 

We are working with Flowers-17 dataset, which is a fine-grained classification challenge. The image dataset is quite small, having only 80 images per class for a total of 1,360 images. A general rule of thumb when applying deep learning to computer vision tasks is to have 1,000-5,000 examples per class, so we are certainly at a huge deficit here.

Here we can do two things.

1) gather more example
2) augment existing images.

As data augmenting is a regularization technique as well, it fits our puropose really well. Let's see how data augmentation performs in this particular case.


### MiniVGGNet with data augmentation

To run the minivggnet model with data augmentation:

`python minivggnet_aug.py --dataset <path to dataset>`

And, the classification report shows the accuracy is a better indeed.

![alt text](minivgg_aug_report.png)

Though, loss-accuracy graph shows there is still hint of overfitting.

![alt text](minivgg_aug.png)


### Transfer Learning

Transfer learning enables utilizing an existing model (with same or different dataset) to a completely different task. In this particular case, we will use VGG16 model (trained with imagenet dataset) as a feature extractor from raw images. Then if we run a Logistic Classifier on this feature vectore, we will find a surprisingly accurate model.

Firstly, for feature extraction:

`python --dataset <path to dataset> --output <location of output hdf5 file>`

We wll end up with a **HDF5** file, which holds the features in hierarchicall structure, and good thing about HDF5 is, we can access data in numpy array like structure which is very convenient to work on.

Now, to train a model on it, we will use [GridSearch](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GridSearchCV.html) to run LogisticClassifier with a grid of available parameter, an this process find the best value. Finally we will again run the classifier with these optimal parameters and check the accuracy.

To do this:

`python train_model.py --db <HDF5 file path> --model <path of out pickle file (saved model)>`


And, here is the classification report.

![alt text](transfer_learn_report.png)


