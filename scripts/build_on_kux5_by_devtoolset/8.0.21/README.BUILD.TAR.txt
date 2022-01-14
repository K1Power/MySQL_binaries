###    Build on K1 Power9 Linux/ K-UX 5.5, 4.18.0-240.10.5.kux.ppc64le with gcc-toolset-9.
### rpcgen not in glibc-common in K-UX
# wget https://github.com/thkukuk/rpcsvc-proto/releases/download/v1.4/rpcsvc-proto-1.4.tar.gz
# tar xzvf rpcsvc-proto-1.4.tar.gz 
# cd rpcsvc-proto-1.4
# CC=gcc \
CFLAGS="-O3 -mcpu=native -mtune=native" \
./configure && make && make install 
# type rpcgen
rpcgen is /usr/bin/rpcgen

### Install some tool and jemalloc for MySQL
# yum groupinstall "Development Tools"
# cd /home/soft/; rpm -Uvh jemalloc-5.2.1-2.el8.ppc64le.rpm jemalloc-devel-5.2.1-2.el8.ppc64le.rpm


### 1. About Build ENV (root/Passw0rd@ips)######################################################################
###    Build on K1 Power9 Linux/ K-UX 5.5, 4.18.0-240.10.5.kux.ppc64le with gcc-toolset-9.
# lscpu |grep -i arch
Architecture:        ppc64le

# uname -r
4.18.0-240.10.5.kux.ppc64le

# uname -m
ppc64le

### 2. Build mysql    ##########################################################################################
### Install gcc-toolset-9
# yum install gcc-toolset-9
# source /opt/rh/gcc-toolset-9/enable
# type gcc
gcc is /opt/rh/gcc-toolset-9/root/usr/bin/gcc
# echo "source /opt/rh/gcc-toolset-9/enable" >> /etc/profile

# yum groupinstall "Development Tools"
# cd /home/soft/; rpm -Uvh jemalloc-5.2.1-2.el8.ppc64le.rpm jemalloc-devel-5.2.1-2.el8.ppc64le.rpm

### Install dependencies
# yum -y install zlib-devel bzip2-devel numactl-devel \
       openssl-devel lz4-devel libxml2-devel wget readline-devel \
       libevent libevent-devel jemalloc-devel libaio-devel git bison cmake cmake3 \
       libtirpc libtirpc-devel numad libzstd libzstd-devel libedit-devel libicu libicu-devel \
       protobuf protobuf-devel

# tar zxvf mysql-boost-8.0.21.tar.gz 
# cd mysql-8.0.21
# mkdir build;cd build;
# cmake3 -LHA .. \
  -DWITH_BOOST=../boost/boost_1_72_0 \
  -DBUILD_CONFIG=mysql_release \
  -DCMAKE_BUILD_TYPE=RelWithDebInfo \
  -DCMAKE_C_COMPILER=`which gcc` \
  -DCMAKE_C_FLAGS="-O3 -mcpu=native -mtune=native -mcmodel=large" \
  -DCMAKE_CXX_COMPILER=`which g++` \
  -DCMAKE_CXX_FLAGS="-O3 -mcpu=native -mtune=native -mcmodel=large" \
  -DCMAKE_INSTALL_PREFIX=/opt/mysql/8.0.21 \
  -DCMAKE_LINKER=`which gcc` \
  -DCMAKE_AR=`which gcc-ar` \
  -DCMAKE_NM=`which gcc-nm` \
  -DCMAKE_RANLIB=`which gcc-ranlib` \
  -DWITH_INNODB_MEMCACHED=1 \
  -DWITH_SSL=system \
  -DWITH_ZLIB=system \
  -DWITH_JEMALLOC=ON \
  -DUSE_LD_LLD=0 \
  -DWITH_NDBCLUSTER=1 \
  -DWITH_SYSTEMD=1 \
  -DWITH_INNODB_MEMCACHED=1 \
  -DJEMALLOC_LIBRARY=/usr/lib64/libjemalloc.so \
  -DWITH_NUMA=ON 2>&1 | tee config.log
# make -j32 VERBOSE=1 2>&1 | tee build.log

### Some useless man files not exists
#CMake Error at man/cmake_install.cmake:89 (file):
#  file INSTALL cannot find
#  "/home/mysql/8.0.21/mysql-8.0.21/man/ndb-common-options.1".
###
# sed -i '88,$d' man/cmake_install.cmake

# make install

# cd /opt/mysql
# tar zcf mysql-community-8.0.21-1.kux55.ppc64le.bin.tar.gz ./8.0.21


