# Neuron Image Classification

## Overview

This project is on the classification of Neuron images produced by the Psychiatry Department of the University of Oxford. The classification task is to learn to distinguish whether Neurons have been treated with the compound [Amyloid-&beta;](https://en.wikipedia.org/wiki/Amyloid_beta "Wikipedia article"). The images used are taken after staining with the [Cy5 dye](https://en.wikipedia.org/wiki/Cyanine "Wikipedia article"), a biomarker for the [MAP2 gene](https://en.wikipedia.org/wiki/Microtubule-associated_protein_2 "Wikipedia article") found in the Neuronal Cytoskeleton. 

Amyloid-&beta; is thought to induce synapse loss. Verifying that this is the case and having a robust classifier will allow researchers to test different compounds for their abilities to reduce the effects of treatment with Amyloid-&beta;

An example of the visualisation produced by the code in this repository:
![Example of the Coloured Saliency Map](https://github.com/wfbstone/Neuron-Image-Classification/blob/master/PNG/Coloured%20Saliency%20Map.png)
Here Red indicates "Treated" and Blue indicates "Untreated". Regions are coloured in Green if the model hasn't produced a confident enough prediction for either class.<sup>[[1](#thresholds)]</sup>

## Installation

To clone this repository run:

```
git clone https://github.com/wfbstone/Neuron-Image-Classification.git
cd Neuron-Image-Classification
```

To install the requirements, run:
```
pip install -r requirements.txt
```
This project was written using iPython 3 on a [Kaggle](https://www.kaggle.com/) kernel. The original kernels can be found [here](https://www.kaggle.com/willstone98/kernels "My kernels on Kaggle").

Any suggestions for improvement are greatly appreciated and I encourage the use and adaptation of my code for use on other projects.
Unfortunately however, the dataset cannot be made public at this time so running the code on the Neuron images is not currently possible.

## The Model

The model used in classification is the [VGG19](https://arxiv.org/pdf/1409.1556.pdf "Paper on arxiv") with weights pre-trained on the [ImageNet](http://www.image-net.org/) dataset as provided by Keras but with 3 Fully-Connected layers of sizes 2048, 2048 and 2 on top of the Convolutional layers. The model was fine-tuned for the task of classifying the Neurons on a training set of 632 images with data augmentation including random cropping and random flipping both horizontally and vertically. After 60 epochs through the training st (15 epochs through each possibility of random flipping) the model performed extremely well on the unaugmented test set - achieving an F1 score of 0.96.

To verify that this performance isn't due to random artifacts in the data, both [Grad-CAM](https://arxiv.org/abs/1610.02391 "Paper on arxiv") and a [Saliency Map](https://arxiv.org/abs/1312.6034 "Paper on arxiv") were implemented.

## Visualisation

For visualising the effects of treatment with Amyloid-&beta; neither Grad-CAM nor the Saliency Map are particularly insightful. Grad-CAM is great for highlighting regions of interest but tends to highlight the entire network of Neurons and the Saliency Map is great at highlighting individual Nuclei and Neurites. 

<a name="thresholds"> Using the Sliding Windows algorithm, the model can be used to classify regions of the image separately. This technique performs well distinguishing which areas look more "Treated" or "Untreated" but the map produced isn’t fine-grained enough to be of use showing which Neurons show the characteristics of having been treated. The Saliency Map on the other hand, is excellent at distinguishing which Neurons are most important towards the overall classification of the image thus a combination of these two techniques produces a useful visualisation of which Neurons are indicative of treatment with A&beta;. </a>

The combination of these two techniques was achieved by colouring the Saliency Map depending on what classification the corresponding region received by the Sliding Windows algorithm. Using [Youden's J Statistic](https://onlinelibrary.wiley.com/doi/epdf/10.1002/1097-0142%281950%293%3A1%3C32%3A%3AAID-CNCR2820030106%3E3.0.CO%3B2-3) to determine where to set the thresholds for confidence, the Saliency Map is coloured Red or Blue if the region is confidently predicted as “Treated” or “Untreated” and Green if the prediction was uncertain.


## Extensions

* Experiment with convolutional implementation of the Sliding Windows algorithm as mentioned in the [OverFeat paper](https://arxiv.org/abs/1312.6229 "Paper on arxiv") in attempt to reduce computation time.
