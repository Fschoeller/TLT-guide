# Nvidia TLT guide:

On the training PC, pull the Nvidia container for TLT: 
```bash
docker pull nvcr.io/nvidia/tlt-streamanalytics:v3.0-dp-py3
```

Next, follow the official guide for TLT: 

```bash
https://docs.nvidia.com/metropolis/TLT/tlt-user-guide/text/quickstart/deepstream_integration.html
```

If, at some point, you get the error `Docker pull failed. 404 Client Error: Not Found (“manifest for nvcr.io/nvidia/tlt-streamanalytics:v3.0-py3 not found: manifest unknown: manifest unknown”)`, run the following command:
``` bash
nvidia-docker tag nvcr.io/nvidia/tlt-streamanalytics:v3.0-dp-py3 nvcr.io/nvidia/tlt-streamanalytics:v3.0-py3
```

For training, the data needs to be in Kitti format, [this](https://github.com/eweill/convert-datasets) repo can be used to convert.

When done training, export the model and copy it to xavier. On xavier, convert the model to trt with the `tlt-converter` script, which can be found [here](https://developer.nvidia.com/tlt-get-started).

If, during convertion, you get the following error: 
```
$ ./tlt-converter -k KEY -d 3,640,640 -o BatchedNMS -c cal.bin -t int8 -i nchw yolov4_resnet18_epoch_080.etlt
[ERROR] UffParser: Validator error: FirstDimTile_2: Unsupported operation _BatchTilePlugin_TRT
[ERROR] Failed to parse the model, please check the encoding key to make sure it's correct
[ERROR] Network must have at least one output
[ERROR] Network validation failed.
[ERROR] Unable to create engine
Segmentation fault (core dumped)
```

Follow step 2 and 3 in [this](https://github.com/NVIDIA-AI-IOT/deepstream_tlt_apps/tree/master/TRT-OSS/Jetson/TRT7.1#3-replace-libnvinfer_pluginso) guide

Detection FPS can be estimated using `trtexec`, e.g: `/usr/src/tensorrt/bin/trtexec --loadEngine=saved.engine --int8 --batch=1 --useSpinWait`
