Build on K1 Power9 Linux, RedHat 7.6 (Kernel 4.14.0-115.8.1.el7a.ppc64le) with devtoolset-7.

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

Install devtoolset-7
# yum install devtoolset-7
source /opt/rh/devtoolset-7/enable
# type gcc
gcc is /opt/rh/devtoolset-7/root/usr/bin/gcc
# gcc --version 
gcc (GCC) 7.3.1 20180303 (Red Hat 7.3.1-5)

# tar zxvf mysql-boost-5.6.51.tar.gz 
# cd mysql-5.6.51
# mkdir build;cd build;
# cmake .. \
  -DBUILD_CONFIG=mysql_release \
  -DCMAKE_BUILD_TYPE=RelWithDebInfo \
  -DCMAKE_C_COMPILER=`which gcc` \
  -DCMAKE_C_FLAGS="-O3 -mcpu=native -mtune=native -mcmodel=large" \
  -DCMAKE_CXX_COMPILER=`which g++` \
  -DCMAKE_CXX_FLAGS="-O3 -mcpu=native -mtune=native -mcmodel=large" \
  -DCMAKE_INSTALL_PREFIX=/opt/mysql/5.6.51 \
  -DCMAKE_LINKER=`which gcc` \
  -DCMAKE_AR=`which gcc-ar` \
  -DCMAKE_NM=`which gcc-nm` \
  -DCMAKE_RANLIB=`which gcc-ranlib` \
  -DWITH_INNODB_MEMCACHED=ON \
  -DWITH_NUMA=ON \
    2>&1 | tee config.log    

# make -j 32 && make install 


# cd /opt/mysql
# tar zcf mysql-community-5.6.51-1.el7.ppc64le.bin.tar.gz ./5.6.51
