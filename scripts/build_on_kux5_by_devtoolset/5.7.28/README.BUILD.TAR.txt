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
# yum -y install zlib-devel bzip2-devel cmake \
    openssl-devel lz4-devel libxml2-devel wget readline-devel \
    libevent-devel libaio-devel bison libtirpc-devel \
    java-1.8.0-openjdk java-1.8.0-openjdk-devel perf numad numactl-devel 

# tar zxf mysql-boost-5.7.28.tar.gz
# cd /home/soft/mysql-5.7.28; mkdir -p build;cd build;
# cmake .. \
  -DBUILD_CONFIG=mysql_release \
  -DCMAKE_BUILD_TYPE=RelWithDebInfo \
  -DCMAKE_C_COMPILER=`which gcc` \
  -DCMAKE_C_FLAGS="-O3 -mcpu=native -mtune=native -mcmodel=large" \
  -DCMAKE_CXX_COMPILER=`which g++` \
  -DCMAKE_CXX_FLAGS="-O3 -mcpu=native -mtune=native -mcmodel=large" \
  -DCMAKE_INSTALL_PREFIX=/opt/mysql/5.7.28 \
  -DCMAKE_LINKER=`which gcc` \
  -DCMAKE_AR=`which gcc-ar` \
  -DCMAKE_NM=`which gcc-nm` \
  -DCMAKE_RANLIB=`which gcc-ranlib` \
  -DWITH_INNODB_MEMCACHED=ON \
  -DWITH_BOOST=../boost/boost_1_59_0 \
  -DWITH_NUMA=ON \
    2>&1 | tee config.log    

# make -j 32 && make install | tee make_install.log

# cd /opt/mysql
# tar zcf mysql-community-5.7.28-1.kux55.ppc64le.bin.tar.gz ./5.7.28
