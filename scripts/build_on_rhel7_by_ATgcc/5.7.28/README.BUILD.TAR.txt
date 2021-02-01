Build on K1 Power9 Linux, RedHat 7.6 (Kernel 4.14.0-115.8.1.el7a.ppc64le) with advance-toolchain-at11.0

### 1. About Build ENV #########################################################################################

# lscpu |grep -i arch
Architecture:          ppc64le
Model name:            POWER9 (architected), altivec supported

# uname -r
4.14.0-115.8.1.el7a.ppc64le

# uname -m
ppc64le

### 2. Build mysql    ##########################################################################################

Install dependencies
# yum -y install zlib-devel bzip2-devel numactl-devel \
    openssl-devel lz4-devel libxml2-devel wget readline-devel \
    libevent-devel jemalloc-devel libaio-devel git bison cmake libtirpc-devel numad \
    install java-1.8.0-openjdk iotop dstat perf java-1.8.0-openjdk-devel nmon

Install advance-toolchain-at11.0
# yum install advance-toolchain-at11.0
# export PATH=/opt/at11.0/bin:$PATH
# type gcc
gcc is /opt/at11.0/bin/gcc
[root@db1 src]# gcc --version
gcc (GCC) 7.4.1 20191016 (Advance-Toolchain-at11.0) [revision 277075]

### Need openssh 1.1 when compiling with advance-toolchain-at11.0
### wget -c https://www.openssl.org/source/openssl-1.1.1i.tar.gz
# tar zxf openssl-1.1.1i.tar.gz 
# cd openssl-1.1.1i/
CC=gcc \
CXX=g++ \
CPP=g++ \
CFLAGS="-O3 -mcpu=native -mtune=native " \
CXXFLAGS="-O3 -mcpu=native -mtune=native " \
CPPFLAGS=$CCXXFLAGS  \
LDFLAGS='-ljemalloc ' \
./config --prefix=/opt/openssl
make -j8 VERBOSE=1 && make install 

export PATH=/opt/openssl/bin:$PATH
export LD_LIBRARY_PATH=/opt/openssl/lib:$LD_LIBRARY_PATH

# tar zxvf mysql-boost-5.7.28.tar.gz 
# cd mysql-5.7.28
# mkdir build;cd build;
# cmake .. \
  -DBUILD_CONFIG=mysql_release \
  -DCMAKE_BUILD_TYPE=RelWithDebInfo \
  -DCMAKE_C_COMPILER=`which gcc` \
  -DCMAKE_C_FLAGS="-O3 -mcpu=native -mtune=native -mcmodel=large" \
  -DCMAKE_CXX_COMPILER=`which g++` \
  -DCMAKE_CXX_FLAGS="-O3 -mcpu=native -mtune=native -mcmodel=large" \
  -DCMAKE_INSTALL_PREFIX=/opt/mysql_at/5.7.28 \
  -DCMAKE_LINKER=`which gcc` \
  -DCMAKE_AR=`which gcc-ar` \
  -DCMAKE_NM=`which gcc-nm` \
  -DCMAKE_RANLIB=`which gcc-ranlib` \
  -DWITH_INNODB_MEMCACHED=ON \
  -DWITH_SSL=/opt/openssl \
  -DWITH_ZLIB=system \
  -DWITH_BOOST=../boost/boost_1_59_0 \
  -DWITH_NUMA=ON \
    2>&1 | tee config.log    

# make -j 32 VERBOSE=1 && make install 


# cd /opt/mysql_at && tar zcf mysql-community-5.7.28-1.el7.ppc64le.at11gcc.bin.tar.gz ./5.7.28
