# Build FFmpeg (Windows)

## Requirements

- 3GB of free space
- Windows 7/8.1/10

## Install deps
### Open a **cmd** as Admin

#### Install chocolatey
	@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin

#### Install Git
	choco install git

#### Install python
	choco install python2
	pip install pywin32

#### Install VisualStudio 2017
[Download Visualstudio 2017](https://docs.microsoft.com/en-us/visualstudio/install/use-command-line-parameters-to-install-visual-studio)
> As of September, 2017 (R503915) Chromium requires Visual Studio 2017 update 3.x to build.  
The clang-cl compiler is used but Visual Studio's header files, libraries, and some tools are required.  
Visual Studio Community Edition should work if its license is appropriate for you.  
You must install the “**Desktop development with C++**” component and the “**MFC and ATL support***” sub-component.  

> You must have the version **10.0.17134 Windows 10 SDK** installed. This can be installed separately or by checking the appropriate box in the Visual Studio Installer.

## Build ffmpeg.dll (WITHOUT proprietary codecs)

This is the default behaviour, does not require additional steps, just run it and tadaaaa :tada:...

	git clone https://github.com/iteufel/nwjs-ffmpeg-prebuilt.git
	cd nwjs-ffmpeg-prebuilt

	//Build ffmpeg ia32
	python build_ffmpeg.py --target_arch=ia32

	//Build ffmpeg x64
	python build_ffmpeg.py --target_arch=x64

## Build ffmpeg.dll (WITH proprietary codecs)

The build procedure for Windows is a little bit complex and require additional steps to generate the FFmpeg library. Unfortunately we can not generate the library natively, we need to use a CygWin environment and do a few tricks:

* Clone the script
	```
	git clone https://github.com/iteufel/nwjs-ffmpeg-prebuilt.git
	```

* Download and install the latest CygWin with the following packages :

	```yasm, make, diffutils, pkg-config, git```.

	You can do it with the typical CygWin installation package manager.

* Setup the building environment: assuming you have installed VS2015 with choco in the previous steps, open a Windows console (CMD.exe) and type

	```
	C:\>cd %VS140COMNTOOLS%
	C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\Tools>cd ..\..\VC
	C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC>vcvarsall.bat [amd64, amd64_x86]
	```

	Don't close the console.

* Start CygWin sharing the previous environment variables:

	```
	C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC>cd C:\cygwin64\bin
	C:\cygwin64\bin>START mintty.exe -i /Cygwin-Terminal.ico -
	```

	The CygWin console will be shown.


* From the CygWin console:

	```
  $ cd /cygdrive/drive/path-to-nwjs-ffmpeg-prebuilt
	```
	where ```drive``` is your main drive and ```path-to-nwjs-ffmpeg-prebuilt``` is the path where you cloned the nwjs-ffmpeg-prebuilt script, an example

	```
	$ cd /cygdrive/c/dev/nwjs-ffmpeg-prebuilt
	```
	then run the script with
	```
	$ python build_ffmpeg.py -pc (default host architecture)
	```

	or

	```
	$ python build_ffmpeg.py -ta [ia32|x64] -pc
	```

If you want to build for different architectures you must close CygWin and close the Windows CMD console, and repeat the above process in order to setup the building environment values for the desired architecture.
