project('darknet', 'c')

gpu = false
cudnn = false
cudnn_half = false
opencv = false
avx = false
openmp = false
libso = false
zed_camera = false
zed_camera_v2_8 = false

use_cpp = false
debug = false

arch = '-gencode arch=compute_75,code=[sm_75,compute_75]'

vpath = './src/'
exec = 'darknet'
objdir  = './obj/'

if libso == true
    libnameso = 'libdarknet.so'
    appnameso = 'uselib'
endif

if use_cpp == true
    cc = meson.get_compiler('g++')
else
    cc = meson.get_compiler('gcc')
endif

cpp = 'g++ -std=c++11'
nvcc = 'nvcc'
opts = '-Ofast'
ldflags = '-lm -pthread'
common = '-Iinclude/ -I3rdparty/stb/include'
cflags = '-Wall -Wfatal-errors -Wno-unused-result -Wno-unknown-pragmas -fPIC'

if debug == true
    #opts = -O0 -g
    #opts = -Og -g
    common += '-DDEBUG'
    cflags += '-DDEBUG'
else
    if avx == true
        cflags += '-ffp-contract=fast -mavx -mavx2 -msse3 -msse4.1 -msse4.2 -msse4a'
    endif
endif

cflags += opts

if host_machine.system() != 'windows'
    ldflags += '-lws2_32'
endif

if opencv == true
    common += '-DOPENCV'
    cflags += '-DOPENCV'
    ldflags += 'pkg-config --libs opencv4 2> /dev/null || pkg-config --libs opencv'
    common += 'pkg-config --cflags opencv4 2> /dev/null || pkg-config --cflags opencv'
endif

if openmp == true
    if host_machine.system() == 'darwin'
        cflags += '-Xpreprocessor -fopenmp'
    else
        cflags += '-fopenmp'
    endif
    ldflags += '-lgomp'
endif

if gpu == true
    common += '-DGPU -I/usr/local/cuda/include/'
    cflags += '-DGPU'
    if host_machine.system() == 'darwin'
        ldflags += '-L/usr/local/cuda/lib -lcuda -lcudart -lcublas -lcurand'
    else
        ldflags += '-L/usr/local/cuda/lib64 -lcuda -lcudart -lcublas -lcurand'
    endif
endif

if cudnn == true
    common += '-DCUDNN'
    if host_machine.system() == 'darwin'
        cflags += '-DCUDNN -I/usr/local/cuda/include'
        ldflags += '-L/usr/local/cuda/lib -lcudnn'
    else
        cflags += '-DCUDNN -I/usr/local/cudnn/include'
        ldflags += '-L/usr/local/cudnn/lib64 -lcudnn'
    endif
endif

if cudnn_half == true
    common += '-DCUDNN_HALF'
    cflags += '-DUCDNN_HALF'
endif

if zed_camera == true
    cflags += '-DZED_STEREO -I/usr/local/zed/include'
    if zed_camera_v2_8 == true
        ldflags += '-L/usr/local/zed/lib -lsl_core -lsl_input -lsl_zed'
    else
        ldflags += '-L/usr/local/zed/lib -lsl_zed'
    endif
endif

obj = 'image_opencv.o http_stream.o gemm.o utils.o dark_cuda.o convolutional_layer.o list.o image.o activations.o im2col.o col2im.o blas.o crop_layer.o dropout_layer.o maxpool_layer.o softmax_layer.o data.o matrix.o network.o connected_layer.o cost_layer.o parser.o option_list.o darknet.o detection_layer.o captcha.o route_layer.o writing.o box.o nightmare.o normalization_layer.o avgpool_layer.o coco.o dice.o yolo.o detector.o layer.o compare.o classifier.o local_layer.o swag.o shortcut_layer.o activation_layer.o rnn_layer.o gru_layer.o rnn.o rnn_vid.o crnn_layer.o demo.o tag.o cifar.o go.o batchnorm_layer.o art.o region_layer.o reorg_layer.o reorg_old_layer.o super.o voxel.o tree.o yolo_layer.o gaussian_yolo_layer.o upsample_layer.o lstm_layer.o conv_lstm_layer.o scale_channels_layer.o sam_layer.o'

if gpu == true
    ldflags += '-lstdc++'
    obj += 'convolutional_kernels.o activation_kernels.o im2col_kernels.o col2im_kernels.o blas_kernels.o crop_layer_kernels.o dropout_layer_kernels.o maxpool_layer_kernels.o network_kernels.o avgpool_layer_kernels.o'
endif

objs = objdir / obj
deps = 