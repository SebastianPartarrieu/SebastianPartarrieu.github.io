---
layout: page
title: Through artists' eyes
description: A VR application powered by deep learning built to plunge users into the fascinating worlds of art
img: /assets/img/through_artists_eyes.png
importance: 3
category: work
github: https://github.com/SebastianPartarrieu/ThroughArtistsEyes
---

## A brief overview

This [project](https://github.com/SebastianPartarrieu/MOVIE_Projet) was conducted over a little less than two months, in collaboration with three other Mines ParisTech students. We built an interactive Virtual Reality (VR) application with [Unity](https://unity.com/) allowing the user to navigate between different locations, each taking on the look & feel of a great artist. Once the user was plunged 'into' this 3D version of a painting, he/she plays a short game, trying to guess which artist was used to generate the environment. Once the user guesses correctly, the style slowly changes to a new artist and this goes on until the user has guessed all the styles and environments we've prepared. The immersive experience is meant to be a contemplative one, where one can take time to appreciate the visual qualities of one's surroundings.

Here's a short video including images and gameplay sequences from the application:

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
    <iframe height=338 width=676 src="https://www.youtube.com/embed/FhrUkyMkoBw" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>
</div>

## Technical aspects

The general workflow for the project goes as follows:

- Pick a 360° picture of a real place
- Apply different style transfer algorithms (CycleGAN, fast neural style transfer, 'classic' style transfer) and compare results
- Integrate the new stylized picture in Unity using a skybox
- Add interactions and transitions in Unity.

Of course, these steps (surprisingly, even the first one) take time and effort and perhaps warrant some explanations. I'll detail the style transfer step as I spent most of my time during the project working on this, as well as finding it to be the most challenging/interesting part of the project.

### Style transfer

This is what I primarily worked on during the project. The first algorithm used was an adapted and re-trained [CycleGAN](https://github.com/junyanz/pytorch-CycleGAN-and-pix2pix) deep learning algorithm to perform the style transfer. I first had to download a sufficiently large dataset of 360° images, which is where Flickr's API along with a short python script came in very handy. Then I had to find datasets of paintings of the various artists we wanted to use for the style transfer. I also performed some data augmentation on the paintings for artists where we couldn't find a sufficient amount of paintings (minimal rotations, shifts & other transformations). Once the images were all cleaned up and ready to go, I could finally start tweaking Cycle GAN's architecture and retraining it from scratch in order to generate the best results possible. If you aren't familiar with GANs check this very informative [blog post](https://towardsdatascience.com/understanding-generative-adversarial-networks-gans-cd6e4651a29), before reading [this one](https://machinelearningmastery.com/what-is-cyclegan/) on cycle gans. Hopefully, then, this image won't come as a surprise:

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/cycle_gan.png' | relative_url }}" alt="" title="example image"/>
    </div>
</div>
<div class="caption">
    CycleGAN: learning mappings between two spaces with the help of cycle-consistency and discriminator functions
</div>

For the purposes of our project, X is the space of 360° images we picked (formally that is a subset of $$ \mathbb{Re}^{2048\times1024\times3} $$) and Y is a subset of $$ \mathbb{R}^{256\times256\times3} $$ of paintings of a specific artist. The space dimensions are just the image dimensions, the 360° images were 2048x1024 across with the three RGB channels and the paintings were generally 256x256. Here are some examples of images used:

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ok_miami1.jpeg' | relative_url }}" alt="" title="example image"/>
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/cezanne.jpg' | relative_url }}" alt="" title="example image"/>
    </div>
</div>
<div class="caption">
    Left a 360° panoramic image and right a painting by Gauguin
</div>
Training a cyleGAN (basically) amounts to learning the complex, non linear, relationship between these two image spaces. Once these mappings have been learnt, we can use the generators (G and F) to perform style transfer on the 360° images, (or try and create realistic 360° images from paintings). We can note that the discriminators, $$ D_X $$ and $$ D_Y $$ are 70x70 Patch discriminators, greatly facilitating the use of this algorithm between two image spaces don't have the same dimensions. Indeed these PatchGAN output probabilities of each patch being 'real', therefore the G function doesn't necessarily need to output something of the same dimension as the images in Y (there are some shortcomings, though which we'll get into below). This is useful as we would ultimately like to keep the 2048x1024x3 dimensions of our 360° images, but perform the style transfer nonetheless.

Aside from picking the datasets and doing some preprocessing and data augmentation, I also tried changing some architectural parameters of the CycleGAN. This consisted in adding some convolutional layers and residual blocks, the added convolutions with stride 2 to downsample the images further (as we were working with larger images than the authors) and residual blocks just to make sure we couldn't drive up performance further. Of course, here, performance is a tricky question. You would like to think that properly trained discriminators ensure that with enough epochs, CycleGAN gives you a satisfactory mapping and adequate style transfer capabilities.

Of course, there are multiple limitations to this approach. The first is that on large images such as the ones used during this project, the PatchGAN discriminator means that you only need to fool the discriminator locally. This brings about repeated motifs once we apply G to the 360° images after training that seem to be the result of this patched approach.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ok_miami1_fake_ukiyoe.png' | relative_url }}" alt="" title="example image"/>
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ukiyoe_example.jpg' | relative_url }}" alt="" title="example image"/>
    </div>
</div>
<div class="caption">
    Left the same 360° picture above once we've applied style transfer that's been trained on a Ukiyoe (genre of Japanese art) collection of paintings as Y. Right one example of the collection.
</div>

The repeated artefacts in the sky and across the image seem to stem from this need to locally fool the discriminator. Another substantial limitation was the long training time as well as extensive computational resources needed to run CycleGAN. This made evaluating small architectural changes a hassle and running any sort of cross-validation a near-impossible task. There are, of course, other limitations and interesting things I ran into whilst implementing CycleGAN during this project but this post seems a little too long already... Instead here are some cool results CycleGAN generated on the 360° dataset coupled with different artist datasets, once I'd tweaked the architecture, performed data augmentation and ran it multiple times changing hyperparameters (Keep in mind that the 360° images are better visualized with a VR headset and the results are more immersive and impressive with the headset):

<div class="row justify-content-sm-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ok_mountain1.jpg' | relative_url }}" alt="" title="example image"/>
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ok_mountain1_fake_ukiyoe.png' | relative_url }}" alt="" title="example image"/>
    </div>
</div>
<div class="row justify-content-sm-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ok_eso5.jpg' | relative_url }}" alt="" title="example image"/>
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ok_eso5_fake_cezanne.png' | relative_url }}" alt="" title="example image"/>
    </div>
</div>
<div class="row justify-content-sm-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ok_paysage_52.jpg' | relative_url }}" alt="" title="example image"/>
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ok_paysage_52_fake_monet_test.png' | relative_url }}" alt="" title="example image"/>
    </div>
</div>
<div class="row justify-content-sm-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ok_pont1.jpeg' | relative_url }}" alt="" title="example image"/>
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ok_pont1_fake_vangogh.png' | relative_url }}" alt="" title="example image"/>
    </div>
</div>
<div class="caption">
    Examples of applying CycleGAN to the 360° dataset with various artist datasets. Left original images and right once CycleGAN was applied for style transfer. Can you guess the artists used for each? <sub>(In order: ukioye (artistic current), cezanne, monet and vangogh)</sub>
</div>

Another approach I tried out was a more traditional one when it comes to style transfer. Following the approach detailed in this [paper](https://arxiv.org/pdf/1508.06576.pdf). Similar to CycleGAN, I won't give a detailed explanation of how it works here, it's just useful to understand that you're basically solving an optimization problem starting from a white noise image and using gradient descent where the loss function includes two terms, a content loss and a style loss. The content loss is a squared error loss between the feature representations (once you've passed the images through a CNN) of your original and generated images. The style loss is a trickier concept involving the mean-squared distance between the Gram matrices of the feature representations of your style image and the generated image.

The key difference with the CycleGAN approach is that here you solve an optimization problem involving **two** images, a content image (the 360° one in our case) and a style image (a single painting from an artist or artistic current) whereas CycleGAN tries to learn a mapping between two **sets** of images. As a result, this 'vanilla' style transfer method varies alot between (content, style) image pairings meaning we needed to iterate over lots of pairs to find matchings where the result was satisfactory, not even mentioning the hyperparameter tuning which needs to be done to determine appropriate style and content loss weights. Anyway, here are some more results:

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ok_lombard_street.jpeg' | relative_url }}" alt="" title="example image"/>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/munch_scream_resized.jpg' | relative_url }}" alt="" title="example image" width="100" height="200"/>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/lombard_munch1.jpg' | relative_url }}" alt="" title="example image"/>
    </div>
</div>
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ok_miami1.jpeg' | relative_url }}" alt="" title="example image"/>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/hokusai_style.jpg' | relative_url }}" alt="" title="example image" width="200" height="200"/>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/3_HOKUSAI.jpg' | relative_url }}" alt="" title="example image"/>
    </div>
</div>
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/360_landscape_53.jpg' | relative_url }}" alt="" title="example image"/>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/gauguin_resized_resized_65.jpg' | relative_url }}" alt="" title="example image" width="200" height="200"/>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/arbre_gauguin_2.jpg' | relative_url }}" alt="" title="example image"/>
    </div>
</div>
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/360_landscape_174.jpg' | relative_url }}" alt="" title="example image"/>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/renoir_style.jpg' | relative_url }}" alt="" title="example image" width="200" height="200"/>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/fontaine_renoir.jpg' | relative_url }}" alt="" title="example image"/>
    </div>
</div>
<div class="caption">
    Left are content images, middle are style and right is the resulting image after 'vanilla' style transfer.
</div>

I hope you managed to get through the project overview and this wasn't too boring :smile: !
