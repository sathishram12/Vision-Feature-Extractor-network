Model evaluation
===========================

## Evaluate test set

- Confirm that the `Awesome-Backbones/datas/annotations.txt` tag is ready
- Confirm that `test.txt` and `annotations.txt` under `Awesome-Backbones/datas/` correspond to
- Find the corresponding configuration file under `Awesome-Backbones/models/`
- Modify parameters according to the configuration file explanation, mainly modifying the weight path
- Open terminal and run in `Awesome-Backbones`

```bash
python tools/evaluation.py models/mobilenet/mobilenet_v3_small.py
``` tools/evaluation.py models/mobilenet/mobilenet_v3_small.py
```

## Single image detection

- Open terminal and run in `Awesome-Backbones`

```bash
python tools/single_test.py datasets/test/dandelion/14283011_3e7452c5b2_n.jpg models/mobilenet/mobilenet_v3_small.py
```

**Parameter Description**:

`img`: the path of the single image to be tested

`config`: Model configuration file. Please pay attention to modify the weight path of `data_cfg->test->ckpt` in the configuration file. This weight will be used for prediction.

`--classes-map`: label file corresponding to the data set, default datas/annotations.txt

`--device` : device used for inference, default GPU

`--save-path`: save path, not saved by default

## Batch image detection

```bash
python tools/batch_test.py datasets/test/dandelion models/mobilenet/mobilenet_v3_small.py --show
```

**Parameter Description**:

`path`: The path of the image folder to be detected in batches

`config`: Model configuration file. Please pay attention to modify the weight path of `data_cfg->test->ckpt` in the configuration file. This weight will be used for prediction.

`--classes-map`: label file corresponding to the data set, default datas/annotations.txt

`--device` : device used for inference, default GPU

`--save-path`: save path, not saved by default

`--show'': Whether to display pictures during batch detection

## Video detection

- Open terminal and run in `Awesome-Backbones`

```bash
python tools/video_test.py datas/demo.mp4 models/mobilenet/mobilenet_v3_small.py --show
```

**Parameter Description**:

`video` : The path of the video being tested

`config`: Model configuration file. Please pay attention to modify the weight path of `data_cfg->test->ckpt` in the configuration file. This weight will be used for prediction.

`--classes-map`: label file corresponding to the data set, default datas/annotations.txt

`--device` : device used for inference, default GPU

`--save-path`: save path, not saved by default

`--show` : whether to enable visual preview
