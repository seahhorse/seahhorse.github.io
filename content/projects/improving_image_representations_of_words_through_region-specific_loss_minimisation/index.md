---
author: ['Seah Shao Xuan', 'Lim Ming Chong']
title: 'Improving Image Representations of Words through Region-specific Loss Minimisation'
date: 2024-05-22
draft: false
cover: 
    image: "images/manga109.png"
description: "How can we make text in generated images seem more real?"
summary: "How can we make text in generated images seem more real?"
tags: ["image synthesis", "generative models", "LDM", "loss augmentation"]
ShowToc: true
TocOpen: false
---

*This project post was originally posted [here](https://www.andrew.cmu.edu/course/16-726-sp24/projects/sseah/project/index.html).*

# Text Generation in Images

Generative models have shown remarkable capability in creating high-fidelity representations of semantic objects. 

However, when it comes to generating textual content, especially alphabets within images, these models often falter, and the challenge lies in the inherent difficulty of understanding the significance of accuracy in graphical word representations. 

Typically, semantic representations are mapped to token embeddings, capturing relative relationships between multiple concepts, but not the nuanced context of textual elements within images.

An illustrative example of this challenge can be observed in comic panel generation, where images are often populated with speech and thought bubbles. 
            

{{< image-grid 
    columns="3"
    srcs="images/example_1.png|images/example_2.png|images/example_3.png|images/example_4.png|images/example_5.png|images/example_6.png"
    caps="Letters blur into each other, and cursive text is illegible.|Spelling mistakes are often common and obvious.|Even though the image generated may be realistic, minor spelling errors do occur.|In languages with complex characters, the characters generated are mostly nonsensical.|Even in images which have text that stand out (in this case the neon signs), the model still generates gibberish.|Even simple characters are often not accurately modelled."
>}}


Ensuring legible and accurate text generation within such images is crucial for the effectiveness of generative models in this domain, and therefore presents a novel avenue for model improvement.

## How About Manga?

The [Manga109](http://www.manga109.org/en/) dataset is a collection of individual manga, a form of Japanese comic panel storytelling method that is typically handdrawn and is littered with text. The dataset comprises 109 manga collections annotated with various relevant attributes, including text within panels.

{{< image-grid 
    columns="1"
    srcs="images/manga109.png|images/manga_annotations.jpg"
    caps="Example of a Manga Page in the Manga109 Dataset.|Each text instance can be annotated through its bounding box, stored in an accompanying XML file."
>}}

## Let's Generate Some Manga!

To solve this problem, we can modify the loss function for model training by adopting a region-specific approach. 

Through image segmentation, we isolate the regions within the images that contain text.

This allows a more targeted approach towards learning the representative structures of text, as these regions are information-dense, and the quality of text representations are highly sensitive to fluctuations.

By applying a multiplicative weight to the loss contribution in these regions, we aim to increase the penalty for inaccuracies, thus focusing the denoising process within these critical areas.

{{< image-grid 
    columns="1"
    srcs="images/schematic.png"
    caps="Focusing Loss on Text Specific Regions."
>}}

### Segment The Text Bubbles

While using the default rectangular bounding boxes can be a good proxy, we expect to obtain better results with more accurate demarcations of the text regions.

As a result, we use an off-the-shelf image segmentation tool [Segment Anything](https://segment-anything.com/) to obtain unlabeled segments of an image.

Since these segments could be of anything, in order to match these segments to actual text bubbles:

1. For each manga page, we use Segment Anything to generate a list of segment masks, together with their confidence score.
2. For each provided annotation, we compute the Intersection over Union (IoU) score for each of the masks, and pick the mask with the highest IoU score.
3. To prevent false matches which can potentially be destructive, we only return segment masks if they are above a threshold confidence score (0.01) and if the ratio of areas is within reasonable bounds (0.75 to 1.33).
4. Finally, we erode each mask using a simple kernel, to remove the edges of text bubbles which are common in manga, and then dilate it again to capture the boundaries of each word character.

With the following algorithm, we are able to obtain reasonably accurate masks for each manga page.

{{< image-grid 
    columns="1"
    srcs="images/manga_masks.jpg|images/manga_text.jpg"
    caps="This approach gives reasonably accurate text bubbles that capture just the text in the image.|Upon applying the mask on the original image, we can extract only text from the image, and convert it into a binary mask for later use."
>}}

### First, Unconditional Diffusion

With the computation of text masks for each manga image, we finetune a pretrained denoising diffusion probabilistic model (DDPM) on this dataset.
We first finetune a baseline DDPM using a simple reconstruction loss, and then proceed to adapt the loss by increasing the proportion of loss attributed to text regions.
To do so, a simple change was made: ```F.mse_loss(img, target)``` was modified to ```F.mse_loss(img, target) + F.mse_loss(img * mask, target * mask)```

So, how did these images turn out?

The first set of experiments on the DDPM with and without additional masked loss produces fairly reasonable results, although the effect of this loss seems to increase the frequency of text instead. This seems reasonable, since adding an additional component of loss that just comes from text data could have the effect of increasing the density of text in each image.

{{< image-grid 
    columns="1"
    srcs="images/ddpm_no_mask_loss.png|images/ddpm_with_mask_loss.png"
    caps="Without additional masked loss.|With additional masked loss. This seems to have two effects: increase in density of text, as well as the complexity of text generated."
>}}

### Now, Conditional Diffusion

While unconditional manga generation may be interesting, a more practical use of this method would be to guide the diffusion process into generating images that are specified by a conditioning prompt.

Therefore, we also finetune a conditional LDM by passing in a prompt containing the text of choice for the generated manga.

To do so, we used ```A manga page containing the text: "(text input)"``` as a conditioning prompt, where the image generation task was further simplified by providing the annotation with only the longest sequence length into the prompt.

If no text was present in the manga, the conditioning prompt defaults to ```A manga page.```

In sum, we generate images based on the following 6 prompts, selected to represent varied complexity. Words that are likely to appear in manga dialogue were chosen.

1. A manga page
2. A manga page containing the text: "あ"
3. A manga page containing the text: "ほら"
4. A manga page containing the text: "マジかよ"
5. A manga page containing the text: "今日何する？"
6. A manga page containing the text: "パンとか食べる？"

{{< image-grid 
    columns="3"
    srcs="images/no_mask_0.png|images/no_mask_1.png|images/no_mask_2.png|images/no_mask_3.png|images/no_mask_4.png|images/no_mask_5.png"
    caps="Prompt: A manga page|Prompt: A manga page containing the text: 'あ'|Prompt: A manga page containing the text: 'ほら'|Prompt: A manga page containing the text: 'マジかよ'|Prompt: A manga page containing the text: '今日何する？'|Prompt: A manga page containing the text: 'パンとか食べる？'"
>}}

These images as well as those that follow are generated from a [pretrained LDM](https://huggingface.co/runwayml/stable-diffusion-v1-5) that can be found on [Hugging Face](https://huggingface.co/). Note that most of the images fail to generate much text, and we get ellipses ("...") instead. This is expected, since Hugging Face themselves [mention that the model itself "cannot render legible text"](https://huggingface.co/runwayml/stable-diffusion-v1-5#limitations) as part of its limitations.

### The Problem with Latent Diffusion Models

In an LDM, an encoder-decoder pair is first trained on the dataset of images in order to extract a latent representation of each image.

The noising and denoising process is then performed on the latent vectors, which lead to better performance.

However, this presents a novel problem in our approach, since the diffusion process is now in the latent space rather than in the pixel space.

To overcome this issue, we try two different approaches to augment the loss for regions which contain text.

#### Approach 1: The Naive Approach

When we encountered this problem, we first tried a naive approach towards encoding mask information in the latent space. 

- The amplitude of each mask was first increased by a factor of 8, as the Gaussian blur in the next step lowers the amplitude.
- A Gaussian blur was then applied to each mask.
- The mask was then resized to the size of the latent space and used for loss augmentation, in a similar fashion to the unconditional DDPM model.

The rationale behind the naive approach was based on our assumption that each element in the latent vector had a localized receptive field over the original image, thus to mask those parts of the image, we just needed to mask all latent elements that included those parts in their receptive field.

{{< image-grid 
    columns="3"
    srcs="images/naive_latent_mask_0.png|images/naive_latent_mask_1.png|images/naive_latent_mask_2.png|images/naive_latent_mask_3.png|images/naive_latent_mask_4.png|images/naive_latent_mask_5.png"
    caps="Prompt: A manga page|Prompt: A manga page containing the text: 'あ'|Prompt: A manga page containing the text: 'ほら'|Prompt: A manga page containing the text: 'マジかよ'|Prompt: A manga page containing the text: '今日何する？'|Prompt: A manga page containing the text: 'パンとか食べる？'"
>}}

The text generated was mostly incorrect and highly deformed, and did not correspond to any Japanese text. The quality of the text/images here do not seem to be any higher than the baseline above. We concluded that our assumption when designing the naive approach was too strong, and may not have held for our chosen LDM model.

#### Approach 2: A Better Approach

The assumption seemed a little too strong, and after going back to the foundations behind neural networks and thinking intuitively about how they work, we were able to devise a second method that had better grounding and would also hopefully produce better results.

- First, mask the image and pass this **masked image** into the encoder to encode it into latent space.
- Activations of high magnitude are likely to correspond to activations that semantically represent textual information, since we only pass in text. Therefore, exponentiate this latent vector.
- Use this strictly positive mask as a multiplicative factor to augment our loss in the latent space, i.e., ```F.mse_loss(img, target) + F.mse_loss(img * mask_latents, target * mask_latents)```

These images were generated by passing the masked image through the encoder, and using the exponents of these activations as a scaling factor to augment the loss function.

Logically, this method makes more sense, since the activations that have high magnitude are likely the ones that correspond to text features.

{{< image-grid 
    columns="3"
    srcs="images/better_latent_mask_0.png|images/better_latent_mask_1.png|images/better_latent_mask_2.png|images/better_latent_mask_3.png|images/better_latent_mask_4.png|images/better_latent_mask_5.png"
    caps="Prompt: A manga page|Prompt: A manga page containing the text: 'あ'|Prompt: A manga page containing the text: 'ほら'|Prompt: A manga page containing the text: 'マジかよ'|Prompt: A manga page containing the text: '今日何する？'|Prompt: A manga page containing the text: 'パンとか食べる？'"
>}}

Despite the fact that the text generated does not correspond to the text provided in the prompt, we do see that there are certain words that are correctly generated, and both the quality and quantity of text in the images is significantly higher.

Many of the generated characters have a high degree of accuracy, which seems to suggest that this method works much better than the previous naive approach as well as the baseline.

## The Challenges Ahead

A considerable challenge at first was the extraction of text from manga. 

Although we first considered using text boxes, we soon realized that they left in a lot of additional noise, which could hamper the loss augmentation process.

This is why, in the end, we decided to use a separate image segmentation tool, and developed our own algorithm to produce better text masks.

Another significant challenge that we encountered was how to use the produced text mask information in augmenting the loss in latent space.

This was difficult since the latent space has obscured semantic meaning, and required us to think of new approaches bring the text mask into latent space.

Ultimately, we believe that this methods works well and that it may be in the right direction for training generative LDMs that produce better text.

Nonetheless, we still believe that this is the right direction for improving the text generation abilities of generative models. 

Since these regions are information-dense to begin with, placing higher emphasis on the loss function for these areas will therefore result in better text generation qualities.

