---
layout: post
title: "Hyperspherical Variational Auto-Encoders"
date: 2023-04-06 11:12:00-0400
description: Dive into VAEs with non Gaussian distributions
bibliography:
tags: math code papers
---


<div class="row justify-content-sm-center">
        <img class="img-fluid rounded z-depth-1" zoomable=true src="{{ '/assets/img/hyperspherical_VAE_stable_diffusion.jpeg' | relative_url }}" alt="" title="H-VAE" width="400" height="200"/>
    </div>
<div class="caption">
    Hyperspherical variational autoencoders (generated using Stable Diffusion)
</div>


"**OMG**, have you seen [insert latest OpenAI GPT model], isn't it amazing !???"

For all those who are getting a little irritated at the sheer quantity of noise in the recent hype cycle, and of all the former web3.0/crypto-heads now going "all-in" on AI: you've come to the right place. Let's forget about the hype and about [how we're all supposedly going to die](https://time.com/6266923/ai-eliezer-yudkowsky-open-letter-not-enough/), heck let's even forget about the transformer architecture and GPT-style generative models. Instead, why don't we lay back, do some math, and have a look at another type of probabilistic generative model - the variational autoencoder (VAE). Don't worry, this won't be the million-th blog post explaining how these work, instead I'll be diving into a recent(ish) flavor of VAE: the [Hyperspherical VAE](https://arxiv.org/pdf/1804.00891.pdf). These modify the prior and posterior to use non-Gaussians distributions such as the von Mises-Fisher (vMF) distributions, which can be interesting when we expect the latent structure to exhibit some hyperspherical properties. Plus, not only does hyperspherical latent structure *sound* cool but it also gives some neat visualizations. So, if you have 5 mins of your precious time to spare, we can go through the paper together and I'll show you a few fun experiments we were able to implement which allowed us to play with the original code and try to bump performance a little. 

# A *very* brief primer on VAEs
If you're still reading at this point, I'm guessing you weren't bored by the ML jargon above and hopefully this means you also already kind of know how a VAE works. For our purposes, a VAE looks something like this:

<div class="row justify-content-sm-center">
        <img class="img-fluid rounded z-depth-1" zoomable=true src="{{ '/assets/img/VAE_blog.png' | relative_url }}" alt="" title="VAE" width="500" height="300"/>
    </div>
<div class="caption">
    Simplified VAE
</div>

You take input data $$X \in \mathbb{R}^{n \times d}$$ ($$n$$ samples and $$d$$ dimensions) which you represent by a latent variable $$Z$$. Now, in a traditional autoencoder, $$Z \in \mathbb{R}^{n \times d_{latent}}$$ will usually be the output of a neural network that simply has an output dimension $$d_{latent}$$ which is smaller than $$d$$. You compress the data to a latent space with fewer dimensions, then de-compress it using a neural network decoder back into the original space. The *variational* framework changes this by considering **probabilistic representations** for $$X$$ and $$Z$$. This means each input will be encoded into a distribution rather than a single value. You may be wondering at this point, why is using probabilities interesting and why should we care about having a latent distributional encoding?

Well, if we consider $$X$$ to follow a certain distribution, we can frame the problem as maximizing the log-likelihood of the data $$\log p_{\tau}(x)$$ where $$\tau$$ parametrizes our input data distribution. This is interesting as if we have the "optimal" parameter that adequately describes our input distribution, then we can sample from the input space directly which means generating new images (hence the name probabilistic generative model). Since the images will be drawn from the "same" distribution as our training set, hopefully this means these won't look like random noise. The problem is that we don't know how to optimize $$p_{\tau}(x)$$ directly. Let's imagine an input space of images of cats, you can't really say "let's find the best mean and stdev for a multi dimensional gaussian that fits these images" - no gaussian distribution would ever fit that remotely well. Instead, we can consider a latent variable $$Z$$ that generates our observations. The nice thing is, we can imagine this latent variable to follow any distribution we would like. This means if we can reframe the optimization of our likelihood to include terms with respect to our latent variable, we have a fighting chance of being able to include reasonable prior assumptions and actually compute the thing.

More mathematically, consider $$\log p_{\tau}(x)$$ within the variational framework: we marginalize over the latent space distribution $$p_{\tau}(x) = \int_{z}^{} p_{\tau}(x, z) \,dz$$ and rewrite this is as $$p_{\tau}(x) = \int_{z}^{} p_{\tau}(x \| z)p_{\tau}(z) \,dz$$. Without going into further detail (see [Wiki](https://en.wikipedia.org/wiki/Variational_autoencoder#:~:text=Variational%20autoencoders%20are%20probabilistic%20generative,first%20and%20second%20component%20respectively.)), you can optimize a lower bound of the log of this integral by maximizing the Evidence Lower Bound (ELBO) $$\mathbb{E}_{q_{\psi}(z \| x)}[ \log p_{\theta}(x \| z)] - KL(q_{\psi}(z \| x) \| p_{\tau}(z))$$. Here, $$q_{\psi}(z \| x) $$ represents our approximate posterior distribution (which is why we use q and not p). This approximate posterior is taken from a family of distributions (e.g. gaussian or vMF as we will see later) and we parametrize it by $$\psi$$. In practice this parametrization refers to the encoder neural network that will output the parameters of the posterior distribution given an input.

-> link to the fact that we use the vMF as the prior on our latent distribution instead of a gaussian
## vMF distribution
The von-Mises Ficher distribution is interesting to consider as a prior for a number of reasons. First, it is not a gaussian. This may sound kind of dumb but honestly, it is not a bad first-order approximation to say that sometimes the best you can do to try and improve a certain method is to where the algorithm relies on the assumption that the underlying distribution is gaussian and **ditch** that. A little more seriously, the von-Mises Fisher is adapted for hyperspherical distributions.

## KL divergence and vMF sampling 

## Modifying the sampling mechanism from the original paper

# Some experiments

## MNIST reconstruction
(Yes, we're *still* using MNIST for experiments, even in 2023...)


## Having a look at the latent space


*Originally, this was a short project carried out by [Emma Bou Hanna](https://www.linkedin.com/in/emma-bou-hanna/) & me (Sebastian Partarrieu) for the [Computational Statistics course](https://www.master-mva.com/cours/computational-statistics/) of the MVA master's degree.*