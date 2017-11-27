I3D models transfered from Tensorflow to PyTorch
================================================

This repo contains several scripts that allow to transfer the weights from the tensorflow implementation of I3D
from the paper [*Quo Vadis, Action Recognition? A New Model and the Kinetics Dataset*](https://arxiv.org/abs/1705.07750) by Joao Carreira and Andrew Zisserman to PyTorch.

The original (and official!) tensorflow code can be found [here](https://github.com/deepmind/kinetics-i3d/).

The heart of the transfer is the `i3d_tf_to_pt.py` script

Launch it with `python i3d_tf_to_pt.py --rgb` to generate the rgb checkpoint weight pretrained from ImageNet inflated initialization.

To generate the flow weights, use `python i3d_tf_to_pt.py --flow`.

You can also generate both in one run by using both flags simultaneously `python i3d_tf_to_pt.py --rgb --flow`.

## Demo

There is a slight drift in the weights that impacts the predictions, however, it seems to only marginally affect the final predictions, and therefore, the converted weights should serve as a valid initialization for further finetuning.

This can be observed by evaluating the same sample as the [original implementation](https://github.com/deepmind/kinetics-i3d/).

For a demo, launch `python i3d_pt_demo.py --rgb --flow`.
This script will print the scores produced by the pytorch model.

Pytorch Flow + RGB predictions:
```
46.114471435546875 1.0 playing cricket
25.701736450195312 1.3641490159699288e-09 hurling sport
24.62874984741211 4.66519378683472e-10 catching or throwing baseball
23.871143341064453 2.1869826782872082e-10 catching or throwing softball
23.611040115356445 1.686102368836373e-10 hitting baseball
1.33902359008789 1.7384399475517398e-11 playing tennis
```

Tensorflow Flow + RGB predictions:
```
1.0 41.8137 playing cricket
1.49717e-09 21.494 hurling sport
3.84311e-10 20.1341 catching or throwing baseball
1.54923e-10 19.2256 catching or throwing softball
1.13601e-10 18.9153 hitting baseball
8.80112e-11 18.6601 playing tennis
```



PyTorch RGB predictions:
```
[playing cricket]: 0.999998
[playing kickball]: 3.890206e-07
[catching or throwing baseball]: 2.721246e-07
[catching or throwing softball]: 1.210907e-07
[shooting goal (soccer)]: 1.108749e-07
```

Tensorflow RGB predictions:
```
[playing cricket]: 0.999997
[playing kickball]: 1.33535e-06
[catching or throwing baseball]: 4.55313e-07
[shooting goal (soccer)]: 3.14343e-07
[catching or throwing softball]: 1.92433e-07
```

PyTorch Flow predictions:
```
[playing cricket]: 0.999998
[playing kickball]: 3.890206e-07
[catching or throwing baseball]: 2.721246e-07
[catching or throwing softball]: 1.210907e-07
[shooting goal (soccer)]: 1.108749e-07
```

Tensorflow Flow predictions:
```
[playing cricket]: 0.928604
[hurling (sport)]: 0.0406825
[playing tennis]: 0.00415417
[playing squash or racquetbal]: 0.00247407
[hitting baseball]: 0.00138002
```

## Time profiling

To time the forward and backward passes, you can install [kernprof](https://github.com/rkern/line_profiler), an efficient line profiler, and then launch

`kernprof -lv i3d_pt_profiling.py --frame_nb 16`

This launches a basic pytorch training script on a dummy dataset that consists of replicated images as spatio-temporal inputs.

On my GeForce GTX TITAN Black (6Giga) a forward+backward pass takes roughly 0.25-0.3 seconds.

