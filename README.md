# Tengine Overview

[![GitHub license](http://OAID.github.io/pics/apache_2.0.svg)](./LICENSE)

**Tengine**, developed by **OPEN** AI LAB, is a lite, high-performance, and modular inference engine for embedded device.

For more detail about tengine, please refer to [official page](https://github.com/OAID/Tengine/blob/master/README.md)

# Install/Compile in Ubuntu 16.04

### 1. Download source code

```
git clone https://github.com/ghimiredhikura/Tengine.git
```

Install dependency libraries if you have not installed in your machine

* OpenCV and Protobuff:

	```
	sudo apt install libprotobuf-dev
	sudo apt install libopencv-dev
	```

* Caffe:
Please refer to [http://caffe.berkeleyvision.org/installation.html](http://caffe.berkeleyvision.org/installation.html)

### 2. Prepare config files
* copy config example file

	```
	cp makefile.config.example makefile.config
	cp etc/config.example etc/config
	```

* edit `makefile.config`
	- By default, `CONFIG_ARCH_ARM64` option is valid. But I set it to `CONFIG_ARCH_AMD64` to match my ubuntu machine
	- Caffe option is also enabled, you have to set caffe path. 
	  
	  ```
	  CONFIG_CAFFE_REF=y
	  CAFFE_ROOT = /home/deep/Desktop/SFD_ROOT/caffe
	  ```

* edit `etc/config`
	- Default setting is used as it is, you can change it to match your machine		   	

### 3. Build
	```
	cd ~/tengine
	make
	make test (Optional)
	```

# Tengine Performance

Test Environment
	CPU : Rockchip RK3399.
		Single-core Cortex-A72 up to 3.70GHz
	Operating System : Ubuntu 16.04

* SqueezeNet
```
cd ~/tengine
./build/tests/bin/bench_sqz
```

* MobileNet
```
cd ~/tengine
./build/tests/bin/bench_mobilenet
```
 
|    Model    | Total Time (ms) |
| ----------- | -----------|
| SqueezeNet |  40.92686 |
| MobileNet |  104.32197 |


# Test

### 1. Test MTCNN Tengine implementation

* Single image

	```
	cd ~/tengine/examples/mtcnn
	cmake .
	make
	```

We need caffe model files of MTCNN. They can be downloaded from [MTCNN page](https://github.com/kpzhang93/MTCNN_face_detection_alignment/tree/master/code/codes/MTCNNv1/model).
Tengine MTCNN supoprts new version of caffe model, old version caffe models can be converted using [caffe tools](https://github.com/weiliu89/caffe/tree/ssd/tools).
The already converted model files are kept in the `Tengine/examples/mtcnn/model/` dir. 

	```	
	[usage]: ./MTCNN  <test.jpg>  <model_dir>  [save_result.jpg]
	```	

* Webcam

I wrote `mtcnn_webcam.cpp` for testing MTCNN in webcam mode. To run in webcam mode, replace `test_mtcnn.cpp` in `Tengine/example/mtcnn/CMakeLists.txt` with `mtcnn_webcam.cpp` and compile using cmake. 

	```
	cd ~/tengine/examples/mtcnn
	cmake .
	make

	[usage]: ./MTCNN  <webcam_id>  <model_dir>
	```

* Benchmark 
	- test on RK3399 single core A72@3.70GHz 
	- run one time for warm-up, repeat 30 runs and take the average time.
	- detect parameters: use default values in [Detect Parameters](#detect-parameters)

	|image|img_size|Tengine|
	|-----|--------|-------|
	|4.jpg|474x316|73.42ms|
	|6.jpg|640x480|100.276ms|

![Alt text](examples/mtcnn/result_1.jpg)

### 2. Test ResNet50 Tengine implementation

* cmake & make
```
	cd ~/tengine/examples/resnet50
	cp etc/config.example etc/config	
	cmake .
	make	
```

* test an image
```
	[usage]: ./RESNET <test.jpg>  <model_dir>
```	

* model_dir is the path of your resnet50 models.  