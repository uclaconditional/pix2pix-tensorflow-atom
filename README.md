# Pix2pix for MI_REAS

## Commands:
### To train:
#### Example command:
```
python pix2pix.py --mode train --output_dir cries_train/Pix2pix-frames-480p-every10-noTitle --max_epochs 200 --input_dir /media/conditionalstudio/REAS_MI_1/CriesWhispers/Pix2pix-frames-480p-every10-noTitle --which_direction BtoA
```
#### The command does:
Trains pix2pix with images in /media/conditionalstudio/REAS_MI_1/CriesWhispers/Pix2pix-frames-480p-every10-noTitle to 200 epochs. Saves checkpoint and model in <pix2pix_root>/cries_train/Pix2pix-frames-480p-every10-noTitle. Input image is A|B with B as input and A as target.
#### Options:
##### --mode :
"train" for training and "test" for generating.
##### --output_dir :
Directory that current training's checkpoints and models will be stored.
##### --max_epochs :
Number of epochs to train.
##### --input_dir :
Directory that contains all the images to be trained on.
##### --which_direction :
"BtoA" for B as input (e.g. 20x20 grid) and A as output (e.g. original Persona image). "AtoB" for the reverse.

#### Image preparation:
One input image should contain both 'input' and 'target'. For e.g. with Persona image that is originally 351x256, pix2pix training ready image should be of dimension 701x256. For preprocessing please refer to [pix2pix-pre-processing-atom](https://github.com/uclaconditional/pix2pix-pre-processing-atom).

### To generate:
#### Example command:
```
python pix2pix.py --mode test --output_dir cries_test/Persona-continuous-CriesWhispers2 --input_dir ../Persona/Pix2pix-continuous-colorGrid --checkpoint cries_train/Pix2pix-frames-480p-colorGrid-every10-actually --aspect_ratio 1.371
```
#### The command does:
With images in <pix2pix_root>/../Persona/Pix2pix-continuous-colorGrid as input, Generate from model saved in <pix2pix_root>/cries_train/Pix2pix-frames-480p-colorGrid-every10-actually. Generated images are to be saved in cries_test/Persona-continuous-CriesWhispers2. Aspect ratio of the input/output images is 1.371.
#### Options:
##### --mode :
"test" for testing and "train" for training.
##### --output_dir :
Directory the generated images from this command will be saved in.
##### --input_dir :
Directory that contains the images that will be inputted into pix2pix trained model. More in 'Image preparation' section.
##### --checkpoint :
Directory that contains the checkpoints and trained models that will be used for generation.
##### --aspect_ratio :
For Persona it is 351.0/256.0 which is roughly 1.371. For Cries and Whispers it is 431x256 which is roughly 1.6836. pix2pix is aspect ratio independent for the training process. All the images are rescaled and cropped internally to a square for training.

#### Image preparation:
All images should be in format A|B. For e.g. A|B 701x256 image with A being 351x256 original Persona frame and B being 351x256 20x20 grid image. --which_direction="BtoA" or "AtoB" will be the same as in the command used to do the training and it doesn't need to be explicitly inputted when in mode "test". The target image can be anything and it does not affect the generated result. It will only used to be outputted as "`*-target.jpg`" images for comparison with "`*-output.jpg`".
#### To batch resize the original image files to a smaller size:
To resize the origial film frames from 720p, 480p to a smaller size that will fit well in GPU memory, use Imagemagick. In the directory where the images are located type for e.g.:
```
mogrify -path ./path/to/output/dir --resize 351x256 -quality 100 -format jpg *.jpg
```

#### To combine the generated images into a video file:
Same as [DCGAN-tensorflow-atom](https://github.com/uclaconditional/dcgan-tensorflow-atom#to-combine-the-generated-images-into-a-video-file).

### Directories:
These are the directories where the images used/generated by Pix2pix are stored on Alienware.
#### Inputs:
Also contains the original frames and others used by DCGAN.
```
/media/conditionalstudio/REAS_MI_1/Persona
/media/conditionalstudio/REAS_MI_1/CriesWhispers
```
#### Outputs/Generated:
```
<pix2pix_root>/persona_test
<pix2pix_root>/cries_test
```
These directories contain directories that hold images generated by mode "test" of Persona and CriesAndWhispers respectively.
#### Trained models:
```
<pix2pix_root/persona_train
<pix2pix_root/cries_train
```
These directories contain directories that hold checkpoint and trained models of trainings from Persona and CriesAndWhispers respectively.


----------------------

# pix2pix-tensorflow

Based on [pix2pix](https://phillipi.github.io/pix2pix/) by Isola et al.

[Article about this implemention](https://affinelayer.com/pix2pix/)

[Interactive Demo](https://affinelayer.com/pixsrv/)

Tensorflow implementation of pix2pix.  Learns a mapping from input images to output images, like these examples from the original paper:

<img src="docs/examples.jpg" width="900px"/>

This port is based directly on the torch implementation, and not on an existing Tensorflow implementation.  It is meant to be a faithful implementation of the original work and so does not add anything.  The processing speed on a GPU with cuDNN was equivalent to the Torch implementation in testing.

## Setup

### Prerequisites
- Tensorflow 1.4.1

### Recommended
- Linux with Tensorflow GPU edition + cuDNN

### Getting Started

```sh
# clone this repo
git clone https://github.com/affinelayer/pix2pix-tensorflow.git
cd pix2pix-tensorflow
# download the CMP Facades dataset (generated from http://cmp.felk.cvut.cz/~tylecr1/facade/)
python tools/download-dataset.py facades
# train the model (this may take 1-8 hours depending on GPU, on CPU you will be waiting for a bit)
python pix2pix.py \
  --mode train \
  --output_dir facades_train \
  --max_epochs 200 \
  --input_dir facades/train \
  --which_direction BtoA
# test the model
python pix2pix.py \
  --mode test \
  --output_dir facades_test \
  --input_dir facades/val \
  --checkpoint facades_train
```

The test run will output an HTML file at `facades_test/index.html` that shows input/output/target image sets.

If you have Docker installed, you can use the provided Docker image to run pix2pix without installing the correct version of Tensorflow:

```sh
# train the model
python tools/dockrun.py python pix2pix.py \
      --mode train \
      --output_dir facades_train \
      --max_epochs 200 \
      --input_dir facades/train \
      --which_direction BtoA
# test the model
python tools/dockrun.py python pix2pix.py \
      --mode test \
      --output_dir facades_test \
      --input_dir facades/val \
      --checkpoint facades_train
```

## Datasets and Trained Models

The data format used by this program is the same as the original pix2pix format, which consists of images of input and desired output side by side like:

<img src="docs/ab.png" width="256px"/>

For example:

<img src="docs/418.png" width="256px"/>

Some datasets have been made available by the authors of the pix2pix paper.  To download those datasets, use the included script `tools/download-dataset.py`.  There are also links to pre-trained models alongside each dataset, note that these pre-trained models require the current version of pix2pix.py:

| dataset | example |
| --- | --- |
| `python tools/download-dataset.py facades` <br> 400 images from [CMP Facades dataset](http://cmp.felk.cvut.cz/~tylecr1/facade/). (31MB) <br> Pre-trained: [BtoA](https://mega.nz/#!H0AmER7Y!pBHcH4M11eiHBmJEWvGr-E_jxK4jluKBUlbfyLSKgpY)  | <img src="docs/facades.jpg" width="256px"/> |
| `python tools/download-dataset.py cityscapes` <br> 2975 images from the [Cityscapes training set](https://www.cityscapes-dataset.com/). (113M) <br> Pre-trained: [AtoB](https://mega.nz/#!K1hXlbJA!rrZuEnL3nqOcRhjb-AnSkK0Ggf9NibhDymLOkhzwuQk) [BtoA](https://mega.nz/#!y1YxxB5D!1817IXQFcydjDdhk_ILbCourhA6WSYRttKLrGE97q7k) | <img src="docs/cityscapes.jpg" width="256px"/> |
| `python tools/download-dataset.py maps` <br> 1096 training images scraped from Google Maps (246M) <br> Pre-trained: [AtoB](https://mega.nz/#!7oxklCzZ!8fRZoF3jMRS_rylCfw2RNBeewp4DFPVE_tSCjCKr-TI) [BtoA](https://mega.nz/#!S4AGzQJD!UH7B5SV7DJSTqKvtbFKqFkjdAh60kpdhTk9WerI-Q1I) | <img src="docs/maps.jpg" width="256px"/> |
| `python tools/download-dataset.py edges2shoes` <br> 50k training images from [UT Zappos50K dataset](http://vision.cs.utexas.edu/projects/finegrained/utzap50k/). Edges are computed by [HED](https://github.com/s9xie/hed) edge detector + post-processing. (2.2GB) <br> Pre-trained: [AtoB](https://mega.nz/#!u9pnmC4Q!2uHCZvHsCkHBJhHZ7xo5wI-mfekTwOK8hFPy0uBOrb4) | <img src="docs/edges2shoes.jpg" width="256px"/>  |
| `python tools/download-dataset.py edges2handbags` <br> 137K Amazon Handbag images from [iGAN project](https://github.com/junyanz/iGAN). Edges are computed by [HED](https://github.com/s9xie/hed) edge detector + post-processing. (8.6GB) <br> Pre-trained: [AtoB](https://mega.nz/#!G1xlDCIS!sFDN3ZXKLUWU1TX6Kqt7UG4Yp-eLcinmf6HVRuSHjrM) | <img src="docs/edges2handbags.jpg" width="256px"/> |

The `facades` dataset is the smallest and easiest to get started with.

### Creating your own dataset

#### Example: creating images with blank centers for [inpainting](https://people.eecs.berkeley.edu/~pathak/context_encoder/)

<img src="docs/combine.png" width="900px"/>

```sh
# Resize source images
python tools/process.py \
  --input_dir photos/original \
  --operation resize \
  --output_dir photos/resized
# Create images with blank centers
python tools/process.py \
  --input_dir photos/resized \
  --operation blank \
  --output_dir photos/blank
# Combine resized images with blanked images
python tools/process.py \
  --input_dir photos/resized \
  --b_dir photos/blank \
  --operation combine \
  --output_dir photos/combined
# Split into train/val set
python tools/split.py \
  --dir photos/combined
```

The folder `photos/combined` will now have `train` and `val` subfolders that you can use for training and testing.

#### Creating image pairs from existing images

If you have two directories `a` and `b`, with corresponding images (same name, same dimensions, different data) you can combine them with `process.py`:

```sh
python tools/process.py \
  --input_dir a \
  --b_dir b \
  --operation combine \
  --output_dir c
```

This puts the images in a side-by-side combined image that `pix2pix.py` expects.

#### Colorization

For colorization, your images should ideally all be the same aspect ratio.  You can resize and crop them with the resize command:
```sh
python tools/process.py \
  --input_dir photos/original \
  --operation resize \
  --output_dir photos/resized
```

No other processing is required, the colorization mode (see Training section below) uses single images instead of image pairs.

## Training

### Image Pairs

For normal training with image pairs, you need to specify which directory contains the training images, and which direction to train on.  The direction options are `AtoB` or `BtoA`
```sh
python pix2pix.py \
  --mode train \
  --output_dir facades_train \
  --max_epochs 200 \
  --input_dir facades/train \
  --which_direction BtoA
```

### Colorization

`pix2pix.py` includes special code to handle colorization with single images instead of pairs, using that looks like this:

```sh
python pix2pix.py \
  --mode train \
  --output_dir photos_train \
  --max_epochs 200 \
  --input_dir photos/train \
  --lab_colorization
```

In this mode, image A is the black and white image (lightness only), and image B contains the color channels of that image (no lightness information).

### Tips

You can look at the loss and computation graph using tensorboard:
```sh
tensorboard --logdir=facades_train
```

<img src="docs/tensorboard-scalar.png" width="250px"/> <img src="docs/tensorboard-image.png" width="250px"/> <img src="docs/tensorboard-graph.png" width="250px"/>

If you wish to write in-progress pictures as the network is training, use `--display_freq 50`.  This will update `facades_train/index.html` every 50 steps with the current training inputs and outputs.

## Testing

Testing is done with `--mode test`.  You should specify the checkpoint to use with `--checkpoint`, this should point to the `output_dir` that you created previously with `--mode train`:

```sh
python pix2pix.py \
  --mode test \
  --output_dir facades_test \
  --input_dir facades/val \
  --checkpoint facades_train
```

The testing mode will load some of the configuration options from the checkpoint provided so you do not need to specify `which_direction` for instance.

The test run will output an HTML file at `facades_test/index.html` that shows input/output/target image sets:

<img src="docs/test-html.png" width="300px"/>

## Code Validation

Validation of the code was performed on a Linux machine with a ~1.3 TFLOPS Nvidia GTX 750 Ti GPU and an Azure NC6 instance with a K80 GPU.

```sh
git clone https://github.com/affinelayer/pix2pix-tensorflow.git
cd pix2pix-tensorflow
python tools/download-dataset.py facades
sudo nvidia-docker run \
  --volume $PWD:/prj \
  --workdir /prj \
  --env PYTHONUNBUFFERED=x \
  affinelayer/pix2pix-tensorflow \
    python pix2pix.py \
      --mode train \
      --output_dir facades_train \
      --max_epochs 200 \
      --input_dir facades/train \
      --which_direction BtoA
sudo nvidia-docker run \
  --volume $PWD:/prj \
  --workdir /prj \
  --env PYTHONUNBUFFERED=x \
  affinelayer/pix2pix-tensorflow \
    python pix2pix.py \
      --mode test \
      --output_dir facades_test \
      --input_dir facades/val \
      --checkpoint facades_train
```

Comparison on facades dataset:

| Input | Tensorflow | Torch | Target |
| --- | --- | --- | --- |
| <img src="docs/1-inputs.png" width="256px"> | <img src="docs/1-tensorflow.png" width="256px"> | <img src="docs/1-torch.jpg" width="256px"> | <img src="docs/1-targets.png" width="256px"> |
| <img src="docs/5-inputs.png" width="256px"> | <img src="docs/5-tensorflow.png" width="256px"> | <img src="docs/5-torch.jpg" width="256px"> | <img src="docs/5-targets.png" width="256px"> |
| <img src="docs/51-inputs.png" width="256px"> | <img src="docs/51-tensorflow.png" width="256px"> | <img src="docs/51-torch.jpg" width="256px"> | <img src="docs/51-targets.png" width="256px"> |
| <img src="docs/95-inputs.png" width="256px"> | <img src="docs/95-tensorflow.png" width="256px"> | <img src="docs/95-torch.jpg" width="256px"> | <img src="docs/95-targets.png" width="256px"> |

## Unimplemented Features

The following models have not been implemented:
- defineG_encoder_decoder
- defineG_unet_128
- defineD_pixelGAN

## Citation
If you use this code for your research, please cite the paper this code is based on: <a href="https://arxiv.org/pdf/1611.07004v1.pdf">Image-to-Image Translation Using Conditional Adversarial Networks</a>:

```
@article{pix2pix2016,
  title={Image-to-Image Translation with Conditional Adversarial Networks},
  author={Isola, Phillip and Zhu, Jun-Yan and Zhou, Tinghui and Efros, Alexei A},
  journal={arxiv},
  year={2016}
}
```

## Acknowledgments
This is a port of [pix2pix](https://github.com/phillipi/pix2pix) from Torch to Tensorflow.  It also contains colorspace conversion code ported from Torch.  Thanks to the Tensorflow team for making such a quality library!  And special thanks to Phillip Isola for answering my questions about the pix2pix code.
