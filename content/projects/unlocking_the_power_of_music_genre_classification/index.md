---
author: ['Seah Shao Xuan', 'Justin Lo Tian Wen']
title: 'Unlocking the Power of Music Genre Classification'
date: 2022-04-27
draft: true
cover: 
    image: "images/manga109.png"
description: "How Music Streaming Services Are Getting Smarter"
summary: "How Music Streaming Services Are Getting Smarter"
tags: ["audio classification", "computer vision", "spectrographs", "convolutional neural network", "recurrent neural network", "long short term memory"]
ShowToc: true
TocOpen: false
---

With the explosion of audio content online, organizing and categorizing music has never been more important. As millions of new tracks are uploaded daily, music streaming services need to be smart about how they recommend songs to listeners. The key to this personalization? Accurately classifying music by genre.

Imagine opening your favorite music app and instantly being presented with songs that perfectly match your mood. That’s the magic of genre classification at work—streaming services are constantly improving how they tag and recommend music, making your listening experience more enjoyable and tailored to your taste.

## Spectrograms: Visualizing Audio

So, how do these services figure out which songs belong to which genres? One powerful technique involves transforming audio files into something called *spectrograms*. These are visual representations of sound that look like images, making it possible to treat the entire genre classification task as an image classification problem.

{{< image-grid 
    columns="1"
    srcs="images/spectrograph.png"
    caps="Spectrographs are good ways to capture audio data as images."
>}}

Spectrograms represent songs as two-dimensional images with:

- **Time** plotted on the x-axis,
- **Frequency** on the y-axis, and
- **Amplitude** or intensity represented through a color spectrum.

Each spectrogram gives us a unique fingerprint of a song, allowing algorithms to "see" and categorize music much like how we recognize objects in photos.

## Data Scraping and Spectrogram Conversion

Every predictive model needs a sizeable data source for it to be trained to an appropriate performance level. Fortunately, music is widely available on the web, and the use of some clever Python packages can help to streamline this process into an easy one.

These are some Python packages that are very helpful for music and audio analysis:
- **[youtube-search](https://pypi.org/project/youtube-search/):** Using a URL library, developers can scrape videos from YouTube with regular expression matching.
- **[pafy](https://pypi.org/project/pafy/):** is a library that enables media to be downloaded from YouTube URLs.
- **[librosa](https://pypi.org/project/librosa/):** A powerful library that converts audio files into spectrograms, making it possible to analyze and classify music.

## Turning Sounds Into Structured Information

Before we can teach a machine to recognize genres, the audio data must be preprocessed to ensure accuracy and consistency:

1. Resizing: Spectrogram images are resized to 64x64 pixels. This standardizes the input, making it easier for algorithms to process.
2. Normalization: The data is normalized with a mean of 0.5 and a standard deviation of 0.5, ensuring the model performs consistently across different audio samples.
3. Balancing: The dataset is trimmed to maintain equal representation of each genre, minimizing bias and improving the fairness of the model.

These steps ensure that the classification model can learn effectively and produce reliable results, giving listeners a seamless and personalized music discovery experience.

Before beginning, it's also important to take a quick look at the data.

{{< image-grid 
    columns="1"
    srcs="images/classical.png|images/jazz.png|images/metal.png|images/techno.png"
    caps="Classical|Jazz|Metal|Techno"
>}}

## Tuning the Knobs and Dials

Creating a reliable model for genre classification isn't just about feeding spectrograms into an algorithm—it's about fine-tuning the process to achieve the best possible performance. This is where training techniques and hyperparameter selection come into play, acting as the secret sauce that elevates a model from good to great.

### Train, Test, and Validate: Building a Solid Foundation

In machine learning, one of the most crucial steps is dividing your data into three distinct sets: training, validation, and testing. The training set is used to teach the model, while the validation set helps fine-tune it. By tweaking the model based on validation results, you ensure it generalizes well to new, unseen data. Finally, the test set is used to evaluate the model’s performance, giving you a clear picture of how well it will perform in real-world scenarios.

### Learning Rate Schedule: Keeping the Model on Track

A well-tuned learning rate can make all the difference. By using a dynamic learning rate schedule, the model can adjust how quickly it learns based on its performance on the validation set. If the model's validation loss improves, the learning rate might stay the same or even increase slightly to speed up training. If the loss plateaus or worsens, the learning rate can decrease, preventing the model from overshooting the optimal solution.

### Early Stopping: Knowing When to Quit

Training a model for too many epochs can lead to overfitting, where the model learns the training data too well and performs poorly on new data. On the flip side, stopping too early can result in underfitting, where the model hasn’t learned enough. Early stopping is a technique that monitors the model’s performance and halts training when no significant improvements are being made, ensuring the model is just right.

## Let's Try It Out!

As a proof of concept, four common genres (Jazz, Classical, Techno, Metal) of music was collected and converted into spectrographs for training, testing and evaluation. These four categories will form the backbone of all subsequent performance analysis hereafter.

### Logistic Regression

Kicking things off with a simple model like logistic regression is a smart way to establish a baseline for performance. While it might seem odd to apply logistic regression to flattened pixel data from spectrograms, it's a useful exercise to gauge how much more complex models might improve on basic performance.

Despite its simplicity, the logistic regression model managed a respectable **test loss of 0.4279** and **test accuracy of 85.43%**. While these numbers set the lower bound, they also highlight the potential gains more sophisticated models could achieve.

### Multilayered Perceptron Model

Next up was the Multilayered Perceptron (MLP), a step up from logistic regression. The MLP introduces hidden layers, which allow the model to capture more complex patterns in the data. Starting with a baseline (no hidden layers), the results were consistent with logistic regression, but adding hidden layers revealed some interesting dynamics:

> | Hidden Layers | Test Loss | Test Accuracy |
> |---------------|-----------|---------------|
> | 0 (Baseline)  | 0.4279    | 85.43%        |
> | 1             | 0.4654    | 84.01%        |
> | 2             | 0.4285    | 83.69%        |

Surprisingly, the model’s performance dipped slightly with one and two hidden layers. This indicates that while additional complexity might help in some cases, it can also introduce challenges, especially if the network isn't properly tuned or if the data isn't sufficiently complex to warrant the extra layers.

### Convolutional Neural Networks Model

Given the nature of spectrograms as image-like data, it makes sense to explore Convolutional Neural Networks (CNNs), which are particularly effective at image classification tasks. CNNs are excellent at extracting spatial patterns from it inputs, which is an essential feature in uncovering hidden patterns in music.

The base CNN performed impressively with a **test loss of 0.2804** and a **test accuracy of 91.53%**. Techniques like 
- Batch Normalization, 
- Max Pooling, and 
- Dropout 

played a significant role in this success, helping the model generalize well to new data.

> | Network   | Test Loss | Test Accuracy |
> |-----------|-----------|---------------|
> | Base CNN  | 0.2804    | 91.53%        |
> | Deep CNN  | 1.2801    | 27.07%        |
> | AlexNet   | 0.4929    | 81.22%        |
> | VGG11 (BN)| 0.7008    | 65.94%        |

Interestingly, the deeper CNN struggled with overfitting, showing that more layers and parameters aren’t always better, especially with a limited dataset. Simpler architectures like AlexNet and VGG11 with Batch Normalization performed better but still fell short of the Base CNN's accuracy.

## Problems with CNNs

While CNNs excel at capturing spatial features in spectrograms, they struggle with temporal data — a critical aspect of music analysis. The challenge lies in the fact that music is not just about frequency patterns; it's also about how these patterns change over time. 

Additionally, the relatively small dataset used here made it difficult for more complex CNNs to learn effectively, leading to overfitting.

To address these limitations, two key strategies can be explored:

1. **Temporal Effects:** Incorporating models that can capture the temporal dynamics of music, such as Recurrent Neural Networks (RNNs) or Long Short-Term Memory (LSTM) networks.

2. **Transfer Learning:** Leveraging pretrained models that have already learned to recognize patterns in vast datasets, thereby jumpstarting the learning process for music genre classification.

## RNN-CNN / LSTM-CNN Models

To better capture the temporal features inherent in music, these CNNs were combined with Recurrent Neural Networks and Long-Short Term Memory models. This hybrid approach allowed the each CNN to extract spatial features from the spectrograms while the RNN or LSTM handled important temporal information, resulting in a more holistic understanding of the audio data.

> | Network    | Test Loss | Test Accuracy |
> |------------|-----------|---------------|
> | CNN + RNN  | 0.2166    | 92.72%        |
> | CNN + LSTM | 0.2608    | 91.31%        |

The combination of CNNs with RNNs yielded the best performance, improving both test loss and accuracy.

## Making Use of Transfer Learning

With the challenge of training deep networks from scratch on a limited dataset, transfer learning is a viable alternative. By starting with a model pretrained on a vast dataset (like ImageNet, with 14 million images), already-learned features and patterns can be used to improve the performance on a specific dataset such as the one on music spectrographs we have here.

There are two methods for transfer learning on a pretrained model: either by moving all weights by running backpropogation on the dataset, or only moving a small subset of them.

### Frozen Weights 

Initially, all layers of the VGG11 model were opened up for finetuning. This approach yielded a **test loss of 0.4917** and a **test accuracy of 80.80%** — a decent performance, but not groundbreaking.

### Full Fine-tuning 

To push the model further, only some layers were allowed to be retrained on the music data. This significantly improved the results, achieving a **test loss of 0.2215** and an impressive **test accuracy of 93.01%**. Fine-tuning allowed the model to adapt to the nuances of music genres, resulting in a highly accurate classifier.

## A Quick Glance at All the Results

| Network           | Pretrained | Test Loss | Test Accuracy |
|-------------------|------------|-----------|---------------|
| Baseline (Log Reg)| No         | 0.4279    | 85.43%        |
| Basic CNN         | No         | 0.2804    | 91.53%        |
| CNN + RNN         | No         | 0.2166    | 92.72%        |
| CNN + LSTM        | No         | 0.2608    | 91.31%        |
| AlexNet           | No         | 0.4929    | 81.22%        |
| AlexNet           | Yes        | 0.4012    | 90.80%        |
| VGG11             | No         | 0.7008    | 65.94%        |
| VGG11 (Frozen)    | Yes        | 0.4917    | 80.80%        |
| VGG11             | Yes        | 0.2215    | 93.01%        |

As music streaming services continue to harness the power of AI and machine learning, the way we discover and enjoy music will only get better. Next time you get a spot-on recommendation, you’ll know there’s some cutting-edge tech—and maybe even a little bit of magic—working behind the scenes.
