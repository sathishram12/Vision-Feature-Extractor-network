Pipeline Visualization
===========================

**Command Line**：

```bash
python tools/vis_pipeline.py \
    ${input} \
    ${CONFIG_FILE} \
    [--skip-type ${SKIP-TYPE}] \
    [--output-dir ${OUTPUT-DIR}] \
    [--phase ${PHASE}] \
    [--number ${NUMBER}] \
    [--sleep ${SLEEP}] \
    [--show ${SHOW}] \
```

**Explanation of all parameters**:

- `input`       : The path to the image folder to be displayed;
- `config`      : Model configuration file path;
- `--skip-type` : Skip some enhancement methods in the pipeline, default ['ToTensor', 'Normalize', 'ImageToTensor', 'Collect'];
- `--output-dir`: Save the enhanced image folder path, which is not saved by default;
- `--phase`     : Specify which stage of the pipeline is displayed. The default is train, which supports ['train', 'test', 'val'];
- `--number`    : Specify the number of pictures to display/save, and all will be displayed by default;
- `--sleep`     : The display time of each picture is 1 second by default;
- `--show`      : Whether to display the enhanced image, not displayed by default.

**示例Step**：

```bash
tools/vis_pipeline.py datasets/test/dandelion configs/swin_transformer/small_224.py --show --number 10 --sleep 0.5 --output-dir aug_results
```

**Notice**:

- It is necessary to preview the data enhancement results of the entire data set. If the proportion of images with occlusion/deformation distortion `occupies a large amount`, it is likely to lead to a decrease in accuracy, because it dominates the direction of training loss. It is recommended to choose an appropriate enhancement method. The configuration file is just the default!

![fail](artifacts/fail03.jpg)
