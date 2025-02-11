# Instructions to Compile multichain
---------------

#### Pre-requisites
-------------------
On RHEL/s390x 
 
Install following packages on RHEL 7.X Server

```sh
yum install gtk2-devel bzip2 binutils libtool git wget curl automake openssl-devel libevent-devel libstdc++-devel gcc-c++  -y
yum install qt5-qttools-devel qt5-qtbase-devel protobuf-devel qrencode.s390x -y
yum install patch -y 
```

On Ubuntu/s390x
Install following packages on Ubuntu 18.04 Server

```apt install libgtk2.0-dev bzip2 binutils git wget curl automake libssl-dev libevent1-dev install 
apt install qttools5-dev* qtbase5-dev-tools libprotobuf-dev qrencode -y```


#### Compiling GCC 6.1 on s390x
---------------
On RHEL/s390x

```
cd $HOME
wget https://ftp.gnu.org/gnu/gcc/gcc-6.1.0/gcc-6.1.0.tar.gz
tar -zxvf  gcc-6.1.0.tar.gz
mkdir -p gccbuild
mkdir -p $HOME/gcc-6.1.0_final 
cd gcc-6.1.0
./contrib/download_prerequisites
cd ../gccbuild/
../gcc-6.1.0/configure --prefix=$HOME/gcc-6.1.0_final/ --enable-shared --disable-multilib --enable-threads=posix --with-system-zlib --enable-languages=c,c++
make
make install
cd $HOME/gcc-6.1.0_final/bin/
export PATH=$PWD:$PATH
cd ../lib64/
export LD_LIBRARY_PATH=$PWD:$LD_LIBRARY_PATH
```

On Ubuntu, no need to build gcc as gcc 6.1.x version is part of the repository. So install it directly.
```
apt install g++-6 -y
apt install gcc-6 -y
update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-6 10
update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-6 10
apt install libtool
```

#### Compiling boost 1.58 from source on s390x
---------------
On RHEL/s390x
```
cd $HOME/
wget https://excellmedia.dl.sourceforge.net/project/boost/boost/1.58.0/boost_1_58_0.tar.gz
tar -zxf boost_1_58_0.tar.gz
cd boost_1_58_0
sudo ./bootstrap.sh —prefix=/usr/
sudo ./b2 cxxflags="-std=c++0x" —prefix=/usr/ install
```
On Ubuntu 18.04, the boost libraries are already available as part of the repository. So install the same using below command
```
apt install libboost-all-dev -y
```
#### Compiling ninja on s390x
---------------

```
cd $HOME/
git clone https://github.com/ninja-build/ninja
cd ninja
git checkout v1.8.2
./configure.py --bootstrap
export PATH=$PWD:$PATH

```

#### Compiling gn on s390x
---------------

```
cd $HOME
git clone https://gn.googlesource.com/gn/
cd gn
export CXX=g++
export CC=gcc
export AR=ar
```
NOTE - 
Delete Identical Code Folding i.e “icf=all” from gn/build/gen.py (search with key `Enable identical code-folding` and delete following 2 lines

``` 
git diff
diff --git a/build/gen.py b/build/gen.py
index ee4f8e1..51aedf5 100755
--- a/build/gen.py
+++ b/build/gen.py
@@ -299,8 +299,6 @@ def WriteGNNinja(path, platform, host, options):
           ldflags.append('-Wl,-strip-all')
 
       # Enable identical code-folding.
-      if not platform.is_darwin():
-        ldflags.append('-Wl,--icf=all')
 
     cflags.extend([
         '-D_FILE_OFFSET_BITS=64',
``` 
 
```
python build/gen.py
ninja -C out 
cd out/
export PATH=$PWD:$PATH
```

#### Compiling Javascript Engine V8 (6.8.290-Branch) on s390x
---------------

```
git clone --depth=1 https://chromium.googlesource.com/chromium/tools/depot_tools.git
cd depot_tools
export PATH=$PATH:$PWD
export VPYTHON_BYPASS="manually managed python not supported by chrome operations"
fetch v8
cd v8
git checkout 6.8.290
gclient sync

### Apply patches listed https://github.com/harsha544/multichain_s390x/tree/master/V8 ###

gn gen out/s390x.release
ninja -C out/s390x.release v8 d8

```

#### Compiling Berkeley DB 4.8 on s390x
---------------

```
cd /data/
git clone https://github.com/bitcoin/bitcoin
cd bitcoin/contrib/
./install_db4.sh `pwd` 
```

#### Compiling Multichain on s390x
---------------
I have formulated a Patch which we shall apply on `2.0-dev` branch.

```
cd /data/
git clone  https://github.com/Multichain/multichain/ 
git clone https://github.com/harsha544/multichain_s390x
cd multichain
git checkout remotes/origin/2.0-release
git reset --hard 7dc4fa82d01003bf5d286e1ce89fa396dc7ce0c5
git apply ../multichain_s390x/01_April_2019
./autogen.sh
./configure LDFLAGS="-L/data/bitcoin/contrib/db4/lib/" CPPFLAGS="-I/data/bitcoin/contrib/db4/include"
make
```



### Cscope Usuage
---------------

cd to the top-level of your project directory and then use a find command to gather up all of the source code files in your project. The following command will recursively find all of the .c, .cpp, .h, and .hpp files in your current directory and any subdirectories, and store the list of these filenames in cscope.files:

```find . -name "*.c" -o -name "*.cpp" -o -name "*.h" -o -name "*.hpp" > cscope.files```

Now, pass the list of source files to Cscope, which will build a reference database:

```cscope -q -R -b -i cscope.files```

Finally, start the Cscope browser:

```cscope -d```

### GDB References
---------------
To Set Breakpoint 

`break wallet/wallet.cpp:3390`

To Print Vaules from Current Frame

https://stackoverflow.com/questions/6261392/printing-all-global-variables-local-variables

### Instructions to push code to local repository
-----------------
```
git clone https://github.com/Multichain/multichain
git clone https://github.com/harsha544/multichain_s390x
cd multichain ; git checkout 2.0-dev
git apply ../multichain_s390x/26_Jun_2019
git add .
git commit -s 
git push
```

