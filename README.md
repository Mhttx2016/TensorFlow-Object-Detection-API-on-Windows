# TensorFlow Object Detection API Train on Custom Dataset for Windows
The deployment of object detection API and training on custom dataset on windows
## 1. Install python3
> Reference:   
> http://blog.csdn.net/zhunianguo/article/details/53524792 
## 2. Install numpy,scipy, matplotlib
> (1). Download numpy.whl scipy.whl from http://www.lfd.uci.edu/~gohlke/pythonlibs/   
> (2). Install  numpy.whl scipy.whl wheel in terminal via: 

        pip3 install *.wheel  
> (3). Open command prompt and make  

        pip3 install matplotlib

## 3. Install tensorflow with GPU support
Reference:   
https://www.tensorflow.org/install/install_windows  
http://www.cnblogs.com/hzm12/p/6422701.html  
https://stackoverflow.com/questions/43942185/failed-to-load-the-native-tensorflow-runtime-python-3-5-2 
### 3.1 Install GPU driver
> (1).Look up the GPU model(eg.Gforce GTX1070) in device manager  
> (2).Download corresponding driver from http://www.nvidia.cn/Download/index.aspx?lang=cn and install by double clicking .exe  

### 3.2 Install CUDA Toolkit8.0  
> (1).Download CUDA8.0 from https://developer.nvidia.com/cuda-toolkit and install by double clicking .exe  
> (2).Validate the installation in cmd terminal via:  

            nvcc -V
>> get information in the picture below if success:  

>> ![image](pics/nvcc.png?raw=true "Optional Title")  

> (3).Add CUDA to System Path  
>> Open environment variables setting(system variable) there are already 'CUDA_PATH' and  CUDA_PATH_V8_0' exist, we need add aditional system variable:   

            CUDA_SDK_PATH = C:\ProgramData\NVIDIA Corporation\CUDA Samples\v8.0 
            CUDA_LIB_PATH = %CUDA_PATH%\lib\x64 
            CUDA_BIN_PATH = %CUDA_PATH%\bin 
            CUDA_SDK_BIN_PATH = %CUDA_SDK_PATH%\bin\win64 
            CUDA_SDK_LIB_PATH = %CUDA_SDK_PATH%\common\lib\x64
>> then,add below to the end of system variable 'PATH':  
 
            ;%CUDA_LIB_PATH%;%CUDA_BIN_PATH%;%CUDA_SDK_LIB_PATH%;%CUDA_SDK_BIN_PATH%;
 >> restart your computer  

### 3.3 Install cuDNN_v5.1
> (1).Download cuDNN_v5.1 from https://developer.nvidia.com/cudnn  
> (2).Copy CuDNN files to Nvidia CUDA toolkit folder  
>> when 3.2 has completed (usually is located on C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v8.0)
>> * copy cudnn\bin\cudnn64_5.dll to C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v8.0\bin\   
>> * copy cudnn\include\cudnn.h to C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v8.0\include\   
>> * copy cudnn\lib\x64\cudnn.lib to C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v8.0\lib\x64\   

### 3.4 Install TensorFlow  
> (1).Install Tensorflow via pip command prompt 

            pip3 install --upgrade tensorflow-gpu
 >**NOTE:** make sure that Visual C++ Redistributate 2015 x64 is installed. If not, download it           
> (2).Validate your installation

            >>> import tensorflow as tf
            >>> hello = tf.constant('Hello, TensorFlow!')
            >>> sess = tf.Session()
            >>> print(sess.run(hello))  
>> If the system outputs the following, then you are ready to begin writing TensorFlow programs:  

            Hello, TensorFlow!
            
## 4. TensorFlow Object Detection API Installation
Reference: https://github.com/tensorflow/models/blob/master/object_detection/g3doc/installation.md 
> **NOTE** Before starting, download the API from https://github.com/tensorflow/models (the whole /models/* directory)
### 4.1 Dependencies   
> Tensorflow Object Detection API depends on the following libraries:  
* Protobuf
* Pillow 
* lxml  
* tf Slim (which is included in the "tensorflow/models" checkout)  
* Jupyter notebook  
* Matplotlib（completed)  
> (1).Protobuf Installation
>> Download protoc-3.3.0-win32.zip from https://github.com/google/protobuf/releases, Copy protoc.exe in directory protoc-3.3.1-win32 to C:\Windows\System32(equivalent add to system variable 'PATH'). Validate via make '>protoc' in cmd prompt.  

> (2) pillow, jupyter Installation  

            pip3 install pillow
            pip3 install jupyter
            
> (3) lxml 
>> Download lxml-3.8.0-cp36-cp36m-win_amd64.whl from http://www.lfd.uci.edu/~gohlke/pythonlibs/#lxml, install via pip3 install *.whl(otherwise may cause import error when import lxml.etree)

### 4.2 Protobuf Compilation  
> The Tensorflow Object Detection API uses Protobufs to configure model and training parameters. Before the framework can be used, the Protobuf libraries must be compiled. This should be done by running the following command from the tensorflow/models directory:  

            # From tensorflow/models/
            protoc object_detection/protos/*.proto --python_out=.
            
### 4.3 Add Libraries to PYTHONPATH  
> When running locally, the tensorflow/models/ and slim directories should be appended to PYTHONPATH. **if there is no PYTHONPATH exist, create system variable with name=PYTHONPATH,value=tensorflow/models/;tensorflow/models/slim** (in order to import thrid party module, we need create PYTHONPATH system variable)

### 4.4 Testing the Installation
> cd to tensorflow/models/ and run  
    
            python3 object_detection/builders/model_builder_test.py
            
## 5. Training on Pascal VOC2012
### 5.1 Configuration
> * Preparing Inputs: Generating the PASCAL VOC TFRecord files according to [preparing_inputs](https://github.com/tensorflow/models/blob/master/object_detection/g3doc/preparing_inputs.md)  
> * Training Pipeline: Configuring the Object Detection Training Pipeline according to [configuring jobs](https://github.com/tensorflow/models/blob/master/object_detection/g3doc/configuring_jobs.md)

### 5.2 Fix compatibility of object_detection for Python3
> Before runing tranin.py with python3, the compatibility should be fixed.   

Reference: https://github.com/tensorflow/models/pull/1610 

> (1) items() and iteritems() issue  

>> In **object_detection/core/batcher.py and object_detection/core/post_processing.py** replace all .iteritems() by .items().For more more detail guidance, look at [here](https://github.com/tensorflow/models/pull/1610/commits/092b1688f3a8cffab691bf95d78d6d11d11373db) or use six.iteritem like [here](https://github.com/tensorflow/models/pull/1610/commits/b9caf04efc32004191813347dcdd5c7296bdca1d).   

> (2) keys() of dict() issue    

>> In **object_detection/core/prefetcher.py** keys() of dict() behaves different between Python 2 and 3, make it explicitly convert to list, for futher reference look at [here](https://github.com/tensorflow/models/pull/1610/commits/86dc50a95ccc6527c7fb24f74df4c7086926d9a5)  

> (3) 'long' is no longer in py3   

>> In **object_detection/utils/ops.py** in line200 and line 202, modify according to below. [further reference](https://github.com/tensorflow/models/pull/1610/commits/86dc50a95ccc6527c7fb24f74df4c7086926d9a5)   

		...
		# if depth < 0 or not isinstance(depth, (int, long)):
		if depth < 0 or not isinstance(depth, int):
			raise ValueError('`depth` must be a non-negative integer.')
		# if left_pad < 0 or not isinstance(left_pad, (int, long)):
		if left_pad < 0 or not isinstance(left_pad, int):
			raise ValueError('`left_pad` must be a non-negative integer.')
		if depth == 0:
			return None
		....   
	
> (4) Division behaves differently   

>> In **object_detection/util/ops.py** line553:  

		# bin_crop_size.append(crop_dim / num_bins)
		bin_crop_size.append(crop_dim // num_bins)
        
> (5) from unittest import mock

>> In **object_detection/core/preprocessor_test.py** Python3 use from unittest import mock instead of Python2's import mock, coment line8 and import as below

		# import mock
		import numpy as np
		import six
		import tensorflow as tf
		from object_detection.core import preprocessor
		from object_detection.core import standard_fields as fields
		if six.PY2:
			import mock # pylint: disable=g-import-not-at-top
		else:
			from unittest import mock # pylint: disable=g-import-not-at-top
			
> (6) Change key of type 'tuple' to 'str'  

>> [Detail modification](https://github.com/tensorflow/models/pull/1593/files)   

>> **Note:** keep using .items()   

### 5.3 Run locally
> [Train an object detection model on a local machine](https://github.com/tensorflow/models/blob/master/object_detection/g3doc/running_locally.md)  

## 6 Training on Customnized Extended Pascal VOC2012 Dateset

### 6.1 Extending Pascal VOC2012
> We add an own class to 20 classes pascal dataset, to do so,   

>> (1) we first annotate our custom images using an annotation software [LabelImg](https://github.com/tzutalin/labelImg). 
>> ![labelimg_demo](pics/labelimg.jpg?raw=true)   

>> (2) Then add our additional class images(.jpg) and corresponding annotation files(.xml, the file name should be same as that of corresponding imgage) to pascal dataset folders <code>\VOCdevkit\VOC2012\JPEGImages</code> and <code>\VOCdevkit\VOC2012\Annotations</code> respectively. 

>> (3) Add the custom class's training imagesets(the file names of xml or jpg), val imagesset and the union of them to the end of <code>\VOCdevkit\VOC2012\ImageSets\Main\train_train.txt</code>,<code>\VOCdevkit\VOC2012\ImageSets\Main\train_train.txt</code> and <code>\VOCdevkit\VOC2012\ImageSets\Main\train_trainval.txt</code> respectively.    

>> (4) regenerate the TFRecord files according to [preparing_inputs](https://github.com/tensorflow/models/blob/master/object_detection/g3doc/preparing_inputs.md) 


## x.x Issues
> 1.TypeError: string argument expected, got 'bytes'  

>> running object_detection/eval.py get error TypeError: string argument expected, got 'bytes'   

>> change line83 of \object_detection\utils\visualization_utils.py   

		# output = six.StringIO()
		output = six.BytesIO()

