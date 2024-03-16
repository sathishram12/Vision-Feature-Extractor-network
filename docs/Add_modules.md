# Add new modules

- A complete model consists of `Backbone`, `Neck`, `Head`, `Loss`, which can be found under the folder `configs`
- Backbone network: usually a feature extraction network, such as ResNet, MobileNet
- Neck: the component used to connect the backbone network to the head, such as GlobalAveragePooling
- Head: components used to perform specific tasks, such as classification and regression
- Loss: used to calculate the deviation between the predicted value and the true value

## Add a new backbone network Backbone

Take `ResNet_CIFAR` as an example

ResNet_CIFAR For CIFAR 32x32 image input, the settings of kernel_size=7, stride=2 in ResNet are replaced with kernel_size=3, stride=1, and the MaxPooling after the stem layer is removed to avoid passing too small feature maps to the residual in the difference block.

It inherits from ResNet and only modifies the stem layer.

1. Create a new file `models/backbones/resnet_cifar.py` under src folder.

    ```python
    import torch.nn as nn

    from ..common import BaseModule
    from .resnet import ResNet


    class ResNet_CIFAR(ResNet):

        """ResNet backbone for CIFAR.

        (a brief description of this backbone network)
        Args:
            depth(int): Network depth, from {18, 34, 50, 101, 152}.
            ...
            (Parameter documentation)
        """

        def __init__(self, depth, deep_stem=False, **kwargs):
            # Call the initialization function of the base class ResNet
            super(ResNet_CIFAR, self).__init__(depth, deep_stem=deep_stem **kwargs)
            # Other special initialization processes
            assert not self.deep_stem, 'ResNet_CIFAR do not support deep_stem'

        def _make_stem_layer(self, in_channels, base_channels):
            # Overload the base class method to modify the network structure
            self.conv1 = build_conv_layer(
                self.conv_cfg,
                in_channels,
                base_channels,
                kernel_size=3,
                stride=1,
                padding=1,
                bias=False)
            self.norm1_name, norm1 = build_norm_layer(
                self.norm_cfg, base_channels, postfix=1)
            self.add_module(self.norm1_name, norm1)
            self.relu = nn.ReLU(inplace=True)

        def forward(self, x):  # Need to return a tuple
            pass  # The forward implementation of the network is omitted here

        def init_weights(self, pretrained=None):
            pass  # If necessary, overload the parameter initialization function of the base class ResNet

        def train(self, mode=True):
            pass  # If necessary, overload the training status function of the base class ResNet
    ```

2. Import the new module in `src/models/backbones/__init__.py`

    ```python
    ...
    from .resnet_cifar import ResNet_CIFAR

    __all__ = [
        ..., 'ResNet_CIFAR'
    ]
    ```

3. Use new backbone network in configuration file under configs folder

    ```python
    model_cfg = dict(
        backbone=dict(
            type='ResNet_CIFAR',
            depth=18,
            other_arg=xxx),
        ...
    ```

## Neck

Take `GlobalAveragePooling` as an example

To add a new neck component, you mainly need to implement the forward function, which performs some operations on the output of the backbone network and passes the results to the head.

1. Create a new file `src/models/necks/gap.py`

    ```python
    import torch.nn as nn


    class GlobalAveragePooling(nn.Module):

        def __init__(self):
            self.gap = nn.AdaptiveAvgPool2d((1, 1))

        def forward(self, inputs):
            # 简单起见，我们默认输入是一个张量
            outs = self.gap(inputs)
            outs = outs.view(inputs.size(0), -1)
            return outs
    ```

2. `在configs/necks/__init__.py` 中导入新模块

    ```python
    ...
    from .gap import GlobalAveragePooling

    __all__ = [
        ..., 'GlobalAveragePooling'
    ]
    ```

3. `Import the new module in src/models/necks/__init__.py`

    ```python
    model_cfg = dict(
        neck=dict(type='GlobalAveragePooling'),
    )
    ```

## Head

Take `LinearClsHead` as an example

To add a new neck component, you mainly need to implement the forward function, which performs some operations on the output of the backbone network and passes the results to the head.

1. Create a new file `src/models/heads/linear_head.py`

    ```python
    from .cls_head import ClsHead

    class LinearClsHead(ClsHead):

        def __init__(self,
                num_classes,
                in_channels,
                loss=dict(type='CrossEntropyLoss', loss_weight=1.0),
                topk=(1, )):
            super(LinearClsHead, self).__init__(loss=loss, topk=topk)
            self.in_channels = in_channels
            self.num_classes = num_classes

            if self.num_classes <= 0:
                raise ValueError(
                    f'num_classes={num_classes} must be a positive integer')

            self._init_layers()

        def _init_layers(self):
            self.fc = nn.Linear(self.in_channels, self.num_classes)

        def init_weights(self):
            normal_init(self.fc, mean=0, std=0.01, bias=0)

        def forward_train(self, x, gt_label):
            cls_score = self.fc(x)
            losses = self.loss(cls_score, gt_label)
            return losses
    ```

2. Import the new module in `src/models/configs/heads/__init__.py`

    ```python
    ...
    from .linear_head import LinearClsHead

    __all__ = [
        ..., 'LinearClsHead'
    ]
    ```

3. Modify the configuration file to use the new neck component, along with the GlobalAveragePooling neck component. The complete model configuration is as follows:

    ```python
    model_cfg = dict(
        backbone=dict(
            type='ResNet',
            depth=50,
            num_stages=4,
            out_indices=(3, ),
            style='pytorch'),
        neck=dict(type='GlobalAveragePooling'),
        head=dict(
            type='LinearClsHead',
            num_classes=1000,
            in_channels=2048,
            loss=dict(type='CrossEntropyLoss', loss_weight=1.0),
            topk=(1, 5),
        ))

    ```

## Add new loss function Loss

To add a new loss function, you mainly need the forward function in the loss function module. In addition, the weighted_loss decorator can be used to conveniently implement a weighted average of the loss of each element.

Suppose we want to simulate a probability distribution generated from another classification model, we need to add L1loss to achieve this purpose.

1. Create a new file `src/models/losses/l1_loss.py`

    ```python
    import torch
    import torch.nn as nn

    from .utils import weighted_loss

    @weighted_loss
    def l1_loss(pred, target):
        assert pred.size() == target.size() and target.numel() > 0
        loss = torch.abs(pred - target)
        return loss

    class L1Loss(nn.Module):

        def __init__(self, reduction='mean', loss_weight=1.0):
            super(L1Loss, self).__init__()
            self.reduction = reduction
            self.loss_weight = loss_weight

        def forward(self,
                    pred,
                    target,
                    weight=None,
                    avg_factor=None,
                    reduction_override=None):
            assert reduction_override in (None, 'none', 'mean', 'sum')
            reduction = (
                reduction_override if reduction_override else self.reduction)
            loss = self.loss_weight * l1_loss(
                pred, target, weight, reduction=reduction, avg_factor=avg_factor)
            return loss
    ```

2. Import the new module in `src/models/losses/__init__.py`

    ```python
    ...
    from .l1_loss import L1Loss, l1_loss

    __all__ = [
        ..., 'L1Loss', 'l1_loss'
    ]
    ```

3. Modify the `loss` field in the configuration file to use the new loss function

    ```python
    loss=dict(type='L1Loss', loss_weight=1.0))
    ```
