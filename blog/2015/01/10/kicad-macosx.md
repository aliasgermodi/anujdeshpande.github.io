KiCad on Mac OS X 10.10
=====

EDA stands for electronic design automation. It consists of a set of tools which are part of a workflow for designing integrated circuits and PCBs. Tools for design as well as analysis exist in a suite. 
Some of the industrial standards are OrCAD from Cadence,
Altium, Eagle from CadSoft, etc. None of them are open source, nor are
they free. They either have a freemium model or a free trial thing
going on. Not suitable for doing Open Hardware at all.  
KiCad is an open source (GNU GPL v2) EDA tool which recently has
gained momentum (again). It's not up to the mark as the above
tools. Eagle has found a sweet spot in the maker movement because it
offers a hobby version. CERN has taken an interest in taking the
development forward for KiCad and hopefully this'll add more
functionality and make it a de facto standard.  

In this tutorial I am going to show how to install it from the source
on a Mac OS X. It's a bit tricky as there is no official .dmg
available (there are some old binaries available, but nothing worth
checking out).  
KiCad devs use GNU Bazaar for version control. It's similar to git. So
we are going to install some dependencies before getting the source
code from launchpad.

	brew install glew bzr cmake

You also need Xcode tools installed. You can do that from the App
Store app.

	mkdir KiCad
	bzr branch lp:kicad
	wget https://sourceforge.net/projects/wxwindows/files/3.0.2/wxWidgets-3.0.2.tar.bz2

Uncompress the downloaded archive

	mv wxWidgets-3.0.2 wx-src
	nano wx-src/src/osx/webview_webkit.mm

Change the following line
    #include <WebKit/WebKit.h>
to

    #include <WebKit/WebKitLegacy.h>

Now run the following :

	kicad/scripts/osx_build_wx.sh wx-src wx-bin kicad 10.10 "-j4"

In case the file isn't executable go to the above directory and

	chmod +x osx_build_wx.sh

This will install wxWidgets for you. Next let's create a build
directory for building the latest code base.

    mkdir build
	cd build
	cmake ../kicad \
      -DCMAKE_C_COMPILER=clang \
      -DCMAKE_CXX_COMPILER=clang++ \
      -DCMAKE_OSX_DEPLOYMENT_TARGET=10.10 \
      -DwxWidgets_CONFIG_EXECUTABLE=../wx-bin/bin/wx-config \
      -DKICAD_SCRIPTING=OFF \
      -DKICAD_SCRIPTING_MODULES=OFF \
      -DKICAD_SCRIPTING_WXPYTHON=OFF \
      -DCMAKE_INSTALL_PREFIX=../bin \
      -DCMAKE_BUILD_TYPE=Release

	make
	make install

Now your directory structure should look something like the
following :

	➜  KiCad  ls -lah
	total 39424
	drwxr-xr-x   9 anuj  staff   306B Jan  9 21:05 .
	drwxr-xr-x  13 anuj  staff   442B Jan  9 18:24 ..
	-rw-r--r--@  1 anuj  staff   6.0K Jan  9 19:04 .DS_Store
	drwxr-xr-x  13 anuj  staff   442B Jan 10 00:00 bin
	drwxr-xr-x  30 anuj  staff   1.0K Jan  9 21:05 build
	drwxr-xr-x  50 anuj  staff   1.7K Jan  9 20:21 kicad
	drwxr-xr-x   6 anuj  staff   204B Jan  9 20:12 wx-bin
	drwxr-xr-x@ 38 anuj  staff   1.3K Jan  9 19:19 wx-src
	-rw-r--r--   1 anuj  staff    19M Oct  7 03:12	wxWidgets-3.0.2.tar.bz2


Your KiCad binaries will in the bin/ directory.

	➜  bin  ls -lah
	total 64
	drwxr-xr-x  13 anuj  staff   442B Jan 10 00:00 .
	drwxr-xr-x   9 anuj  staff   306B Jan  9 21:05 ..
	lrwxr-xr-x   1 anuj  staff    52B Jan  9 21:05 bitmap2component.app -> kicad.app/Contents/Applications/bitmap2component.app
	lrwxr-xr-x   1 anuj  staff    41B Jan  9 21:05 cvpcb.app -> kicad.app/Contents/Applications/cvpcb.app
	drwxr-xr-x  14 anuj  staff   476B Jan  9 21:05 demos
	drwxr-xr-x   4 anuj  staff   136B Jan  9 21:05 doc
	lrwxr-xr-x   1 anuj  staff    44B Jan  9 21:05 eeschema.app -> kicad.app/Contents/Applications/eeschema.app
	lrwxr-xr-x   1 anuj  staff    44B Jan  9 21:05 gerbview.app -> kicad.app/Contents/Applications/gerbview.app
	drwxr-xr-x   3 anuj  staff   102B Jan  9 21:05 kicad.app
	-rw-r--r--   1 anuj  staff   1.3K Jan 10 00:00 noname.pro
	lrwxr-xr-x   1 anuj  staff    50B Jan  9 21:05 pcb_calculator.app -> kicad.app/Contents/Applications/pcb_calculator.app
	lrwxr-xr-x   1 anuj  staff    42B Jan  9 21:05 pcbnew.app -> kicad.app/Contents/Applications/pcbnew.app
	lrwxr-xr-x   1 anuj  staff    45B Jan  9 21:05 pl_editor.app -> kicad.app/Contents/Applications/pl_editor.app

One of the interesting things that you can do with KiCad is
python scripting. In the above steps I haven't mentioned how to build to
enable scripting but that's definitely something one should check out
once you get the hang of KiCad.  
Chris Gammell of Contextual Electronics (one half of the
[Amp Hour](http://www.theamphour.com/) podcast) has a nice YouTube
series on getting started with PCB design using KiCad. It's definitely
a great point for getting to know the nitty gritties of this fantastic
open source tool.  
