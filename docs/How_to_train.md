Train your own data set
===========================

- Confirm that the `Awesome-Backbones/datas/annotations.txt` tag is ready
- Confirm that `train.txt`, `test.txt` and `annotations.txt` under `Awesome-Backbones/datas/` correspond to
- Select the model you want to train and find the corresponding configuration file under `Awesome-Backbones/models/`
- Modify parameters according to `Configuration file explanation`

```bash
python tools/train.py models/mobilenet/mobilenet_v3_small.py
```

**Command Line**:

```bash
python tools/train.py \
    ${CONFIG_FILE}\
    [--resume-from] \
    [--seed] \
    [--device] \
    [--gpu-id] \
    [--split-validation] \
    [--ratio] \
    [--deterministic] \
```

**Explanation of all parameters**:

- `config`: The path to the model configuration file.
- `--resume-from`: Resume training from the point of interruption and provide a weight path. `Be sure to note that the correct recovery method is from Last_Epoch***.pth`, such as --resume-from logs/SwinTransformer/2022-02- 08-08-27-41/Last_Epoch15.pth
- `--seed`: Set the random number seed, which is set according to the environment by default.
- `--device`: Set GPU or CPU training
- `--gpu-id`: Specify the GPU device, the default is 0 (single cards are basically 0 and do not need to be changed)
- `--split-validation`: Whether to divide the validation set from the training set. The division ratio defaults to 0.2, otherwise the test set will be used directly for verification.
- `--ratio`: The ratio of dividing the verification set from the training set, the default is 0.2, and randomly selected from a certain fold of the training set after shuffle
- `--deterministic`: related to multi-GPU training, no need to set it yet