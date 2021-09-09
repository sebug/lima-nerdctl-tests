# Replacing Docker Desktop On Mac
Following along here: https://medium.com/nttlabs/containerd-and-lima-39e0b64d2a59

## First, qemu:

https://wiki.qemu.org/Hosts/Mac

### Getting latest libffi https://github.com/libffi/libffi/releases/download/v3.4.2/libffi-3.4.2.tar.gz

	curl -OL https://github.com/libffi/libffi/releases/download/v3.4.2/libffi-3.4.2.tar.gz
	tar zxf libffi-3.4.2.tar.gz
	cd libffi-3.4.2
	./configure
	make
	sudo make install
	cd ..

### gettext http://www.gnu.org/software/gettext/

	curl -OL https://ftp.gnu.org/pub/gnu/gettext/gettext-0.21.tar.gz
	tar zxf gettext-0.21.tar.gz
	cd gettext-0.21
	./configure
	make
	sudo make install

### pkg-config
(have to do that before glib even if the doc mentions otherwise)

	curl -OL http://pkgconfig.freedesktop.org/releases/pkg-config-0.29.2.tar.gz
	tar zxf pkg-config-0.29.2.tar.gz
	cd pkg-config-0.29.2
	./configure --with-internal-glib
	make
	sudo make install
	cd ..

### autoconf http://www.gnu.org/software/autoconf/autoconf.html

	curl -OL http://ftp.gnu.org/gnu/autoconf/autoconf-2.71.tar.xz
	tar zxf autoconf-2.71.tar.xz
	cd autoconf-2.71
	./configure
	make
	sudo make install
	cd ..

### automake http://www.gnu.org/software/automake/

	curl -OL https://ftp.gnu.org/gnu/automake/automake-1.16.4.tar.xz
	tar zxf automake-1.16.4.tar.xz
	cd automake-1.16.4
	./configure
	make
	sudo make install
	cd ..

### pixman http://www.pixman.org/

	curl -OL https://www.cairographics.org/releases/pixman-0.40.0.tar.gz
	tar zxf pixman-0.40.0.tar.gz
	cd pixman-0.40.0
	./configure
	make
	sudo make install
	cd ..
	
### ninja https://ninja-build.org/

	git clone https://github.com/ninja-build/ninja.git
	cd ninja
	./configure.py --bootstrap
	sudo cp ninja /usr/local/bin

### meson https://mesonbuild.com

Go to the releases page https://github.com/mesonbuild/meson/releases
and download the latest version. You'll have to right click the installer package to tell MacOS that you really want to deal with an unsigned package.

### Latest Python
Download latest Python for some reason.

### Download pcre2

Turns out that wasn't required and did more harm than good. This just to say that I have a version of pcre2-10.37 on my system.


### glib
Using glib-2.56

	curl -OL https://download-fallback.gnome.org/sources/glib/2.56/glib-2.56.4.tar.xz
	tar zxf glib-2.56.4.tar.xz
	./configure --with-pcre=internal
	make
	sudo make install
	cd ..
	

### qemu

In qemu-openpty.c I had to define openpty:

static int openpty(int *amaster, int *aslave, char *name,
struct termios *termp, struct winsize *winp)

we'll see whether that actually works.

	curl -OL https://download.qemu.org/qemu-6.1.0.tar.xz
	tar zxf qemu-6.1.0.tar.xz
	cd qemu-6.1.0
	./configure --python=/Library/Frameworks/Python.framework/Versions/3.9/bin/python3
	make
	sudo make install

### lima

	curl -OL https://github.com/lima-vm/lima/releases/download/v0.6.3/lima-0.6.3-Darwin-x86_64.tar.gz
	tar zxf lima-0.6.3-Darwin-x86_64.tar.gz
	sudo mv bin/* /usr/local/bin
	sudo mv share/doc/lima /usr/local/share/doc/lima
	sudo mv share/lima /usr/local/share/lima
	
Now we can start using it

	limactl start

And then verify it works with

	lima uname

## Creating an image

	lima nerdctl build -t lima-test /Users/sgfeller/Documents/Projets/lima-nerdctl-tests/image-test

	lima nerdctl run -d -p 127.0.0.1:8080:80 lima-test
