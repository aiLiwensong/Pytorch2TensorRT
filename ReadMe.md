- # Pytorch2TensorRT

> 将Pytorch模型部署到TensorRT的一个简单用法，技术路线为“pytorch model-->onnx file-->TensorRT engine”。
>
> 当前仅针对ONNX和TensorRT支持OP可进行转换，如有不支持的OP需编写插件。

# News:

* 2023.11.15: 支持到TRT8，优化int8量化校准集class，优化参数配置；
* 2020.12.10: 更新`trt_convertor.py`脚本，使之适用于TRT7；



## 软件环境：

```
pycuda                   2022.2.2
nvidia-tensorrt              8.4.1.5
nvidia-cuda-runtime-cu11 11.8.89
nvidia-cudnn-cu11        8.9.2.26
```

## 当前支持：

- [x] TensorRT FP32
- [x] TensorRT FP16
- [x] TensorRT INT8

## 使用方法：

1. 从Pytorch模型到ONNX：修改并使用`pytoch_to_onnx.py`脚本转ONNX，或者独自进行转换；
2. 利用自行提供的或根据上一步转换好的ONNX文件，进行TensorRT转换：`Python main.py`，并指定必要的参数;

## 使用示例：

ONNX file to FP16 engine:
`python main.py --batch_size 32 --mode fp16 --onnx_file_path my_files/centernet.onnx --engine_file_path my_files/test_fp16.engine`

ONNX file to INT8 engine:
`python main.py --mode int8 --onnx_file_path my_files/yolov8n.onnx --engine_file_path my_files/yolov8n_int8.engine --imgs_dir imgs_dataset`

## 使用说明：

Pytorch模型转ONNX：

- 参考脚本`pytoch_to_onnx.py`，需按照自己的需要定义模型与输入样例，然后转换。

将ONNX转换为INT8的TensorRT引擎，需要:

1. 准备一个校准集，用于在转换过程中寻找使得转换后的激活值分布与原来的FP32类型的激活值分布差异最小的阈值;
2. 并写一个校准器类，该类需继承trt.IInt8EntropyCalibrator2父类，并重写get_batch_size,  get_batch, read_calibration_cache, write_calibration_cache这几个方法。可直接使用`myCalibrator.py`，需传入图片文件夹地址；


## 参考：

https://github.com/GuanLianzheng/pytorch_to_TensorRT5.git

官方示例：path_to_tensorrt/TensorRT-5.1.5.0/samples/python/int8_caffe_mnist

