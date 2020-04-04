# FRTM-VOS
This repository contains an implementation of the video object segmentation
method FRTM. A detailed description of the method is
found in the CVPR 2020 paper <https://arxiv.org/pdf/2003.00908.pdf>.
If you find the code useful, please cite using:

    @InProceedings{robinson2020learning,
      title={Learning Fast and Robust Target Models for Video Object Segmentation},
      author={Robinson, Andreas and Lawin, Felix J{\"a}remo and Danelljan, Martin and Khan, Fahad Shahbaz and Felsberg, Michael},
      booktitle = {{IEEE} Conference on Computer Vision and Pattern Recognition},
      month = {June},
      year={2020},
      publisher = {Computer Vision Foundation},
    }


## Installation
Clone the repository: `git clone https://github.com/andr345/frtm-vos.git`

Create a conda environment and install the following dependencies:
```shell script
sudo apt install ninja-build  # For Debian/Ubuntu
conda install -y cython pip scipy scikit-image tqdm
conda install -y pytorch torchvision cudatoolkit=10.1 -c pytorch
pip install opencv-python easydict
```

PyTorch 1.0.1 is slightly faster. If you wish to try to this version, replace the `conda install pytorch` above
with the following:
```shell script
conda install pytorch==1.0.1 torchvision==0.2.2 -c pytorch
pip install "pillow<7"
```

## Datasets

### DAVIS

To test the DAVIS validation split, download and unzip the 2017 480p trainval images and annotations here:
<https://davischallenge.org/davis2017/code.html>.

Or, more precisely, [this file](https://data.vision.ee.ethz.ch/csergi/share/davis/DAVIS-2017-trainval-480p.zip).

### YouTubeVOS

To test our validation split and the YouTubeVOS challenge 'valid' split, download [YouTubeVOS 2018](https://youtube-vos.org/dataset/)
and place it in this directory structure:

```
/path/to/ytvos2018
|-- train/
|-- train_all_frames/
|-- valid/
`-- valid_all_frames/
```
You only actually need 300 sequences of `train/` and `train_all_frames/` and these are listed
in `lib/ytvos_jjvalid.txt`. Thanks to Joakim Johnander for providing this split.

## Models

These pretrained models are available for download: 

| Name            | Backbone  | Training set       | Weights  |
|-----------------|:---------:|:------------------:|:--------:|
| rn18_ytvos.pth  | ResNet18  | YouTubeVOS         | [download](https://drive.google.com/open?id=1anOEzUMxXR4ff2qaUJNojAABWuAmaGvw) |
| rn18_all.pth    | ResNet18  | YouTubeVOS + DAVIS | [download](https://drive.google.com/open?id=1t21DG1ts-2NQXDVvuQjW9LY9VVkYuXU5)
| rn101_ytvos.pth | ResNet101 | YouTubeVOS         | [download](https://drive.google.com/open?id=1KFg7ZjdJyhLE58WzEBlznOrDpKmQqviC) |
| rn101_all.pth   | ResNet101 | YouTubeVOS + DAVIS | [download](https://drive.google.com/open?id=1GqaB80sznVkonprCdYhURwGwqiPRhP-v) |
| rn101_dv.pth    | ResNet101 | DAVIS              | [download](https://drive.google.com/open?id=1gRFn2NojH47BjURSws2XIyuTjzFkmuSV) |


The script `weights/download_weights.sh` will download all models and put them in the folder `weights/`.

## Running evaluations

Open `evaluate.py` and adjust the `paths` dict to your dataset locations and where you want the output.
The dictionary is found near line 110, and looks approximately like this:

```python
    paths = dict(
        models=Path(__file__).parent / "weights",  # The .pth files should be here
        davis="/path/to/DAVIS",  # DAVIS dataset root
        yt2018="/path/to/ytvos2018",  # YouTubeVOS 2018 root
        output="/path/to/results",  # Output path
    )
```

Then try one of the evaluations below. The first run will pause for a few seconds while compiling a
PyTorch C++ extension.

Scripts for generating the results in the paper:
```shell script
python evaluate.py --model rn101_ytvos.pth --dset yt2018val       # Ours YouTubeVos 2018
python evaluate.py --model rn101_all.pth --dset dv2016val         # Ours DAVIS 2016
python evaluate.py --model rn101_all.pth --dset dv2017val         # Ours DAVIS 2017

python evaluate.py --model rn18_ytvos.pth --fast --dset yt2018val # Ours fast YouTubeVos 2018
python evaluate.py --model rn18_all.pth --fast --dset dv2016val   # Ours fast DAVIS 2016
python evaluate.py --model rn18_all.pth --fast --dset dv2017val   # Ours fast DAVIS 2017

```

`--model` is the name of the checkpoint to use in the `weights` directory.

`--fast` reduces the number of optimizer iterations to match "Ours fast" in the paper.

`--dset` is one of

  | Name        | Description                                                |
  |-------------|------------------------------------------------------------|
  | dv2016val   | DAVIS 2016 validation set                                  |
  | dv2017val   | DAVIS 2017 validation set                                  |
  | yt2018jjval | Our validation split of YouTubeVOS 2018 "train_all_frames" |
  | yt2018val   | YouTubeVOS 2018 official "valid_all_frames" set            |

## Training
Code for training will be uploaded soon.

## Contact
Andreas Robinson

email: andreas.robinson@liu.se

Felix Järemo Lawin

email: felix.jaremo-lawin@liu.se
