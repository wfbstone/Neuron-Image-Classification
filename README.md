# Neuron Image Classification

## Overview

This project is on the classification of Neuron images produced by the Psychiatry Department of the University of Oxford. The classification task is to learn to distinguish whether Neurons have been treated with the compound [Amyloid- &beta;](https://en.wikipedia.org/wiki/Amyloid_beta "Wikipedia article"). The images used are taken after staining with the [Cy5 dye](https://en.wikipedia.org/wiki/Cyanine "Wikipedia article"), a biomarker for the [MAP2 gene](https://en.wikipedia.org/wiki/Microtubule-associated_protein_2 "Wikipedia article") found in the Neuronal Cytoskeleton. 

Amyloid-&beta; is thought to induce synapse loss. Verifying that this is the case and having a robust classifier will allow researchers to test different compounds for their abilities to reduce the effects of treatment with Amyloid-&beta;

An example of the visualisation produced by the code in this repository:
![Example of the Coloured Saliency Map](https://github.com/wfbstone/Neuron-Image-Classification/blob/master/PNG/Coloured%20Saliency%20Map.png)
Here Red indicates "Treated" and Blue indicates "Untreated". Regions are coloured in Green if the model hasn't produced a confident enough prediction for either class.<sup>[[1](#thresholds)]</sup>

## Installation

To clone this repository run:
```
git clone https://github.com/wfbstone/Neuron-Image-Classification.git
cd DeepLearningFlappyBird
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

To produce an effect mapping, the Sliding Windows algorithm is implemented convolutionally as mentioned in the [OverFeat paper](https://arxiv.org/abs/1312.6229 "Paper on arxiv"). The Pooling layers within the VGG19 mean that this technique doesn't give as fine-grained an output as the full Sliding-Windows algorithm but given the fast run time it works fine. This technique is better at distinguishing which regions look more "Treated" or "Untreated" but not the individual Neurons. 

Combining this technique with the Saliency Map the produces a very useful visualisation of the effect of treatment with Amyloid-&beta;. Colouring the Saliency Map depending on which class the Sliding Windows algorithm deemed that region to be allows the visualisation of which Neurites appear to look treated by the compound and which don't. 

<a name="thresholds">This map also includes the option of "Unsure" and to include this, [Youden's J Statistic](https://en.wikipedia.org/wiki/Youden%27s_J_statistic "Wikipedia article") is computed from the [ROC Curve](https://en.wikipedia.org/wiki/Receiver_operating_characteristic "Wikipedia article") and the optimal threshold is the one which maximises the J Statistic.</a>

## Extensions

* Improve robustness of sliding windows classification to reduce number of contradictory results. e.g.
![Example of contradictory result](https://github.com/wfbstone/Neuron-Image-Classification/blob/master/PNG/Coloured%20Saliency%20Map%20-%20Contradictory.png)
* Experiment with pooling and padding the outputted sliding windows map: this map doesn't directly correlate with which regions should be classified as what (there is overlap etc.)
