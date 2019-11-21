# tiny-tensorrt
一个非常高效易用的nvidia TensorRT封装,支持c++,python调用,支持caffe,onnx,tensorflow模型.

# 注意事项
TensorRT更新了6.0的版本,api做了许多修改,所以我也升级tiny-tensorrt到6.0版本,5.x的版本在trt-5.1.5.0分支

但是请注意新的特性和更新不会同步到5.x分支,所以我建议使用最新的版本

# 里程碑
- [ ] uff支持 -- 现在已经可以用了,但是官方的感觉还是不太好用,我在尝试继续做简化 :underage::underage::underage:
- [x] 自定义onnx模型输出节点 :fire::fire::fire: ---2019.10.18
- [x] 升级到TensorRT 6.0.1.5 --- 2019.9.29
- [ ] 实现更多的层(有需要请给我提issue阿喂,但是我希望是通用的层) --working on
- [x] caffe模型支持
- [x] PRELU支持
- [x] upsample支持
- [x] 引擎序列化
- [x] caffe model int8支持
- [x] onnx支持
- [x] python api支持
- [ ] 有想法做个量化校准工具,但是目前没有很好的idea
- [x] 在p4显卡上测试
- [x] 自定义使用显卡

# 系统需求
TensorRT 6.x 版本和 cuda 10.0+

如果要使用python api, 那么还需要安装python2/3 和 numpy

# 使用方法
c++
```c++
#include "Trt.h"

Trt trt;
// create engine and running context, note that engine file is device specific, so don't copy engine file to new device, it may cause crash
trt.CreateEngine("path/to/sample.prototxt",
                 "path/to/sample.caffemodel",
                 "path/to/engineFile", // since build engine is time consuming,so save we can serialize engine to file, it's much more faster
                 "outputblob",
                 calibratorData,
                 maxBatchSize,
                 runMode);
// trt.CreateEngine(onnxModel,engineFile,maxBatchSize); // for onnx model

// you might need to do some pre-processing in input such as normalization, it depends on your model.
trt.DataTransfer(input,0,True); // 0 for input index, you can get it from CreateEngine phase log output, True for copy input date to gpu

//run model, it will read your input and run inference. and generate output.
trt.Forward();

//  get output.
trt.DataTransfer(output, outputIndex, False) // you can get outputIndex in CreateEngine phase
// them you can do post processing in output
```

python
```python
import sys
sys.path.append("path/to/where_pytrt.so_located/")
import pytrt

trt = pytrt.Trt()
trt.CreateEngine(prototxt, caffemodel, engineFile, outputBlobName, calibratorData, maxBatchSize, mode)
# trt.CreateEngine(onnxModel, engineFile, maxBatchSize)
# see c++ CreateEngine

trt.DoInference(input_numpy_array) # slightly different from c++
output_numpy_array = trt.GetOutput(outputIndex)
# post processing
```

also see [tensorrt-zoo](https://github.com/zerollzeng/tensorrt-zoo), it implement some common computer vision model with tiny tensor_rt, it has serveral good samples

# 支持的额外层
- 自定义尺度upsample,在yolov3上测试
- yolo-det, 就是yolov3的最后一层,将三个尺度的输出集合起来产生检测结果
- PRELU, 在openpose和mtcnn上做了测试

# 致谢
这个项目最初是因为看到了[lewes6369/tensorRTWrapper](https://github.com/lewes6369/tensorRTWrapper) 和 [lewes6369/TensorRT-Yolov3](https://github.com/lewes6369/TensorRT-Yolov3),所以打算做一个更好的.

然后我使用了[spdlog](https://github.com/gabime/spdlog),可以有比较好看的log信息,看起来更清晰一点

使用了pybind11来做python api

# 版权
这个项目包含了一些第三方模块,对于它们需要遵守他们的版权要求

对于我写的那一部分,你可以做任何你想做的事