# hello-world
Hello World repository for Git tutorial
This is an example repository for the Git tutoial on https://www.w3schools.com

This repository is built step by step in the tutorial.

New line added


# Desciption: This document written by Giapvn aims to guide to setup lib packages for GCS with Ubuntu 18.04 OS on Getac Laptop. Order of installation and version number of libs must be obeyed strictly to secure installing right.

#1. Notice when installing OS
    • Download Ubuntu 18.04 Desktop (64 bit).iso file from this link:
https://ubuntu.com/download/desktop/thank-you?version=18.04.4&architecture=amd64
    • Download Rufus (below link) for boot your USB Flash device in UEFI format
https://github.com/pbatard/rufus/releases/download/v3.9/rufus-3.9.exe
    • Press F12/Delete key for enter USB Boot screen.
    • Note: After restarting your Getac, don’t neglect to enter (when appearing a light blue screen) the password for Secure Boot that you set while you installing OS. Select:
	Enroll MOK > continue > yes > enter your password you set
    • Remove the default version of gstreamer:
	sudo apt-get remove gstreamer1*

#2. Installation of NVIDIA driver for NVIDIA GeForce GTX 1050
	# Check your cards:
	sudo lshw -c display
	# Check nvidia-drivers supporting for your cards:
	sudo ubuntu-drivers devices
	# Update the latest drivers and install:
	sudo apt-add-repository ppa:graphics-drivers/ppa
	sudo apt-get update
	sudo ubuntu-drivers autoinstall
	sudo reboot
	# Check installing successfully:
	nvidia-smi

#3. Installation of CUDA 10.1 Update 1 and Cudnn in corresponding version
	# Go to /gpu folder.
	# Run in turn these command-lines below:
	sudo dpkg -i cuda-repo-ubuntu1804-10-1-local-10.1.168-418.67_1.0-1_amd64.deb
	sudo apt-key add /var/cuda-repo-10-1-local-10.1.168-418.67/7fa2af80.pub
	sudo apt-get update
	sudo apt-get install cuda
	echo "export PATH=/usr/local/cuda-10.1/bin/${PATH:+:${PATH}}" >> ~/.bashrc
	# Check installing CUDA 10.1 Update 1 successfully:
	cd /usr/local/cuda-10.1/samples/1_Utilities/deviceQuery
	sudo make
	./deviceQuery
	# If Result = PASS it's ok.
	# Return /gpu foler:
	# Run in turn these command-lines below:
	sudo dpkg -i libcudnn7_7.6.5.32-1+cuda10.1_amd64.deb
	sudo dpkg -i libcudnn7-dev_7.6.5.32-1+cuda10.1_amd64.deb
	sudo dpkg -i libcudnn7-doc_7.6.5.32-1+cuda10.1_amd64.deb
	# Check installing successfully:
	mkdir temp && cd temp
	sudo cp -r /usr/src/cudnn_samples_v7 ./
	cd ./cudnn_samples_v7/mnistCUDNN
	make clean && sudo make
	./mnistCUDNN
	# If you see "Test passed!" at the end of the output, installing is ok!
	cd ../../.. && sudo rm -r temp

#4. Installation of Gstreamer
	# Go to /gst folder.	
	# Install dependencies:
	sudo apt-get update
	sudo apt-get install -y autoconf
	sudo apt-get install -y autopoint
	sudo apt-get install -y libtool
	sudo apt-get install -y flex
	sudo apt-get install -y libglib2.0-dev
	sudo apt-get install -y libssl-dev
	sudo apt-get install -y yasm
	sudo apt-get install -y libva-dev
	# Install bison >= 2.4:
	cd bison-3.4
	sudo ./configure && sudo make -j8
	sudo make install
	sudo ln -s /usr/local/bin/bison /usr/bin/bison
	# Install Gstreamer:
	cd ../gstreamer/gstreamer
	./autogen.sh && ./configure && make -j8 && sudo make install && sudo ldconfig
	cd ../gst-plugins-base
	./autogen.sh && ./configure && make -j8 && sudo make install && sudo ldconfig
	cd ../gst-plugins-good
	./autogen.sh && ./configure && make -j8 && sudo make install && sudo ldconfig
	cd ../gst-plugins-bad
	./autogen.sh && ./configure && make -j8 && sudo make install && sudo ldconfig
	cd ../gst-plugins-ugly
	./autogen.sh && ./configure && make -j8 && sudo make install && sudo ldconfig
	cd ../gst-libav
	./autogen.sh && ./configure && make -j8 && sudo make install && sudo ldconfig
	cd ../gst-omx
	./configure --disable-gtk-doc --with-omx-target=generic
	cd ../gstreamer-vaapi
	./autogen.sh && ./configure && make -j8 && sudo make install && sudo ldconfig
	# Check installed version of Gstreamer:
	gst-inspect-1.0 --gst-version -a

#5. Installation of OpenCV with full contributions on C/C++
	# Go to /opencv folder.
	# Edit install-opencv.sh if necessary (No installation of libprotobuf-dev and protobuf-compiler).
	# Start installing:
	sudo chmod +x install-opencv.sh
	./install-opencv.sh
	# Check version of OpenCV:
	pkg-config --modversion opencv

#6. Installation of TensorFlow C/C++ API
	# Install dependencies
	sudo apt-get update
	sudo apt-get install python-dev python-pip python3.6-dev python3-pip
	pip3 install -U --user pip six numpy wheel setuptools mock 'future>=0.17.1'
	pip3 install -U --user keras_applications==1.0.6 --no-deps
	pip3 install -U --user keras_preprocessing==1.0.5 --no-deps
	# Go to /tf folder.
	# Setup bazel build tool:
	chmod +x bazel-0.25.1-installer-linux-x86_64.sh
	sudo ./bazel-0.25.1-installer-linux-x86_64.sh
	# Setup TensorFlow r1.14.0
	tar -xzf v1.14.0.tar.gz
	cd tensorflow-1.14.0
	git clone https://github.com/abseil/abseil-cpp.git
	ln -s abseil-cpp/absl ./absl
	./configure
	# **Note: only Yes with CUDA and use python3 and gcc-7 for setting, fix version of CUDA is 10.1 and of cuDNN is 7.6. And then build with bazel as follow:
	bazel build --config=noaws --config=nogcp --config=nohdfs --config=noignite --config=nokafka --config=cuda --config=opt //tensorflow:libtensorflow_cc.so
	# **Note: Insert this command-line:
	--jobs 1 --local_resources 2048,0.5,1.0 –verbose_failures
	# if there is not capability of resources
	# After installing completely, configure LD_LIBRARY_PATH to .so file in /tensorflow-1.14.0/bazel-genfiles/tensorflow into /etc/ld.so.conf.d/
