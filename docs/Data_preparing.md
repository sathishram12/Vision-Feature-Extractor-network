Data Preparing
===========================

1. Label file production

    - This demonstration takes the flower data set as an example. The directory structure is as follows：

        ```{Notes}
        ├─flower_photos
        │  ├─daisy
        │  │      100080576_f52e8ee070_n.jpg
        │  │      10140303196_b88d3d6cec.jpg
        │  │      ...
        │  ├─dandelion
        │  │      10043234166_e6dd915111_n.jpg
        │  │      10200780773_c6051a7d71_n.jpg
        │  │      ...
        │  ├─roses
        │  │      10090824183_d02c613f10_m.jpg
        │  │      102501987_3cdb8e5394_n.jpg
        │  │      ...
        │  ├─sunflowers
        │  │      1008566138_6927679c8a.jpg
        │  │      1022552002_2b93faf9e7_n.jpg
        │  │      ...
        │  └─tulips
        │  │      100930342_92e8746431_n.jpg
        │  │      10094729603_eeca3f2cb6.jpg
        │  │      ...
        ```

    - Create the tag file `annotations.txt` in `src/datas/`, and write the `category name index` to the file line by line;

        ```{notes}
        daisy 0
        dandelion 1
        roses 2
        sunflowers 3
        tulips 4
        ```

2. Data set partitioning
    - Open `src/tools/split_data.py`
    - Modify the `original dataset path` and the `divided save path`. It is strongly recommended that the divided save path `datasets` be left unchanged. In the next step, operations will be based on folders by default.

        ```{note}
        init_dataset = 'A:/flower_photos'
        new_dataset = 'A:/Awesome-Backbones/datasets'
        ```

    - Open the terminal under `Awesome-Backbones/` and enter the command:

        ```bash
        python tools/split_data.py
        ```

    - The format of the divided data set is as follows:

        ```{notes}

        ├─...
        ├─datasets
        │  ├─test
        │  │  ├─daisy
        │  │  ├─dandelion
        │  │  ├─roses
        │  │  ├─sunflowers
        │  │  └─tulips
        │  └─train
        │      ├─daisy
        │      ├─dandelion
        │      ├─roses
        │      ├─sunflowers
        │      └─tulips
        ├─...
        ```

3. Data set information file production

- Make sure the divided data set is under `datasets`, and using `ln` command to do soft link to `src/datasets`  if not, modify the data set path under `get_annotation.py`;

    ```{note}
    datasets_path = 'Your dataset path'
    ```

- Open the terminal under `src` folder and enter the command:

    ```bash
    python tools/get_annotation.py
    ```

- Obtain the generated data set information files `train.txt` and `test.txt` under `src/datas`
