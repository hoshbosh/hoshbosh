---
title: "Model Architecture"
date: 2026-04-04
description: "Architecture Breakdown"
draft: false
---
# Basis
The architecture we are using is outlined in [this paper](https://arxiv.org/pdf/2407.05059). Originally, we were 
planning on using a GAN-based model. But, as outlined in the paper, diffusion-based models have proven to be more
clinically relevant.

## Brownian Bridge Diffusion Model
Diffusion consists of two processes, the forward and backwards processes. The forward process consists of applying
noise and gradually destroying information of the image, and the backwards process is the removal of noise from an
image until our synthetic image is all that remains. So when we want to perform an inference, we give the model pure noise 
and it does denoising until an image is present. [This video](https://youtu.be/1pgiu--4W3I?si=qkVO66sTJvy84YhN) does a fantastic job of understanding this process.
Specifically, we use a conditional diffusion model, meaning that we give it a starting point, the model applies noise, then denoises;
in contrast to just denoising from pure noise. Information about denoising is traditionally learned using a U-net backbone.

The Browian Bridge in this context refers to the noising schedule, instead of the end point of the noising step
being complete and pure noise, it still has some information from the input. The Brownian Bridge keeps training
and inferences stable. Brownian Bridge excels in situations where there are known endpoints, in our case, a CT and
and MRI. Additionally, we get the benefit of better signal preservation, since information is never completely destroyed, 
we get better preservation of brain structures.

## Style Key Conditioning and Inter-Slice Tradjectory Alignment Sampling
In addition to the U-net backbone, the paper also applies cross-attention, informing the model of scanner-specific style consistencies. The style
cross-attention is called Style Key Conditioning(SKC) in the paper. In addition to this, the paper also proposes Inter-Slice Trajectory Alignment 
(ISTA) sampling. This technique consists of using co-prediction, using an inference with the model and taking the average of surrounding slices 
so that inter-slice structures are consistent. Then it performs a correction by calling another inference, since this is a sampling technique, it only
applies to inference-time, there are no training trade offs that need to be made here.

