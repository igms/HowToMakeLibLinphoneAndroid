# How to make libLinphone-Android with x264 in MacOS
* Install _yasm_, _nasm_, _ant_, _python_, _cmake_, _ccache_, _pkgconfig_
```shell
brew install yasm
brew install nasm
brew install ant 
brew install python
brew install cmake
brew install ccache
brew install pkgconfig
```


* Download the source code with git.

`git clone git://git.linphone.org/linphone-android.git --recursive`

`cd linphone-android`

`git tag`

`git checkout 3.2.7`

`git submodule update --init --recursive`

* Modify the method enc_req_vfu(MSFilter *f, void *arg) in msx264.c

```C
#define UNUSED(x) (void)(x)

static int enc_req_vfu(MSFilter *f, void *arg){
	EncData *d=(EncData*)f->data;
	d->generate_keyframe=TRUE;
	UNUSED(arg);
	return 0;
}
```

* Set the environment variable

```shell
export PATH=$PATH:/Users/usrname/Library/Android/sdk/tools:/Users/usrname/Library/Android/sdk/platform-tools:/Users/usrname/Library/Android/NDK/android-ndk-r13b

export ANDROID_HOME=/Users/usrname/Library/Android/sdk
```

* Run the python script prepare.py to customize
```shell
./prepare.py -c && ./prepare.py armv7 -DENABLE_NON_FREE_CODECS=YES -DENABLE_G729=YES -DENABLE_FFMPEG=YES -DENABLE_GPL_THIRD_PARTIES=YES -DENABLE_UNMAINTAINED=YES -DENABLE_VIDEO=YES -DENABLE_X264=YES -cc > prepare.log 2>&1
```


* Edit WORK/android-armv7/cmake/EP_x264_configure.sh

Change $CC path 
`($NDK_PATH)/toolchains/llvm/prebuilt/darwin-x86_64/bin/clang` to `($NDK_PATH)/toolchains/arm-linux-androideabi-4.9/prebuilt/darwin-x86_64/bin/arm-linux-androideabi-gcc`

Add `"--disable-asm"` to the last line

* Start to compile
```shell
make > make.log 2>&1
make liblinphone-android-sdk > make-sdk.log 2>&1
```

* Check if TEXTREL issue exist
```shell
$NDK_PATH/toolchains/arm-linux-androideabi-4.9/prebuilt/darwin-x86_64/bin/arm-linux-androideabi-readelf -d ~/Desktop/libmsx264.so
```


