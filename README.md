# Instructions to Compile multichain on RHEL/s390x 
---------------

#### Pre-requisites
---------------
Install following packages on RHEL 7.X Server
```sh
yum install libtool git wget curl automake boost-devel openssl-devel libevent-devel libstdc++-devel gcc-c++  -y
yum install qt5-qttools-devel qt5-qtbase-devel protobuf-devel qrencode.s390x  -y
yum install patch -y 
```

#### Compiling Berkeley DB 4.8 on s390x
---------------

```
cd /data/
git clone https://github.com/bitcoin/bitcoin
cd contrib/ 
./install_db4.sh `pwd` 
```

#### Compiling Multichain on s390x
---------------
To summarize I have formulated a Patch which we shall apply on `2.0-release` branch.

```
cd /data/
git clone  https://github.com/Multichain/multichain/ 
git clone https://github.com/harsha544/multichain_s390x
cd multichain
git checkout remotes/origin/2.0-release
git apply ../multichain_s390x/0001-Applying-BE-changes-on-s390x.patch
./autogen.sh
./configure LDFLAGS="-L/data/bitcoin/contrib/db4/lib/" CPPFLAGS="-I/data/bitcoin/contrib/db4/include"
make
```
