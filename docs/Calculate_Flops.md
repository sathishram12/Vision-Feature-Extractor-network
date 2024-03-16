Model Flops Param
===========================

**Command Line**：

```bash
python tools/get_flops.py ${CONFIG_FILE} [--shape ${Shape}] 
```

**Notice**：

- The official number of parameters and floating point operations are based on ImageNet, which means the default number of categories is `1000`, so when you evaluate your model, please modify `num_classes` to the corresponding number in the configuration file, because it will largely affect the results
- If you have added any `base class` convolution/pooling/sampling functions, please register them in `utils/flops_counter.py/get_modules_mapping()`
