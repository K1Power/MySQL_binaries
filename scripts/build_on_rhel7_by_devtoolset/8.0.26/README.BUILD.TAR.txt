Build on K1 Power9 Linux, RedHat 7.6 (Kernel 4.14.0-115.8.1.el7a.ppc64le) with devtoolset-9.

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
       libevent libevent-devel jemalloc-devel libaio-devel git bison cmake cmake3 \
       libtirpc libtirpc-devel numad libzstd libzstd-devel libedit-devel libicu libicu-devel \
       protobuf protobuf-devel

Install devtoolset-9
# yum install devtoolset-9
source /opt/rh/devtoolset-9/enable
# type gcc
gcc is /opt/rh/devtoolset-9/root/usr/bin/gcc
# gcc --version 
gcc (GCC) 7.3.1 20180303 (Red Hat 7.3.1-5)

/*
/root/mysql_tar/8.0.26/mysql-8.0.26/unittest/gunit/innodb/ut0new-t.cc:58:63: error: ‘(2.00416836000897277799610805135016e-292l + 1.0e+0l)’ is not a constant expression
   static constexpr T INIT_VAL = std::numeric_limits<T>::min() + 1;

use:
  -DWITH_UNIT_TESTS=OFF \
or:
sed -i 's/ut0new/#ut0new/g' ../unittest/gunit/innodb/CMakeLists.txt

CMake Error at man/cmake_install.cmake:86 (file):
  file INSTALL cannot find
  "/root/mysql_tar/8.0.26/mysql-8.0.26/man/ndb_blob_tool.1".

# sed -i '85,$d' man/cmake_install.cmake
*/


# tar zxvf mysql-boost-8.0.26.tar.gz 
# cd mysql-8.0.26
# mkdir build;cd build;
# sed -i 's/ut0new/#ut0new/g' ../unittest/gunit/innodb/CMakeLists.txt
# cmake3 -LHA .. \
  -DWITH_BOOST=../boost/boost_1_73_0 \
  -DBUILD_CONFIG=mysql_release \
  -DCMAKE_BUILD_TYPE=RelWithDebInfo \
  -DCMAKE_C_COMPILER=`which gcc` \
  -DCMAKE_C_FLAGS="-O3 -mcpu=native -mtune=native -mcmodel=large" \
  -DCMAKE_CXX_COMPILER=`which g++` \
  -DCMAKE_CXX_FLAGS="-O3 -mcpu=native -mtune=native -mcmodel=large" \
  -DCMAKE_INSTALL_PREFIX=/opt/mysql/8.0.26 \
  -DCMAKE_LINKER=`which gcc` \
  -DCMAKE_AR=`which gcc-ar` \
  -DCMAKE_NM=`which gcc-nm` \
  -DCMAKE_RANLIB=`which gcc-ranlib` \
  -DWITH_INNODB_MEMCACHED=1 \
  -DWITH_SSL=system \
  -DWITH_ZLIB=bundled \
  -DWITH_JEMALLOC=ON \
  -DUSE_LD_LLD=0 \
  -DWITH_NDBCLUSTER=1 \
  -DWITH_SYSTEMD=1 \
  -DWITH_NUMA=ON 2>&1 | tee config.log
# make -j32 VERBOSE=1 2>&1 | tee build.log
# sed -i '85,$d' man/cmake_install.cmake
# make install

# cd /opt/mysql
# tar zcf mysql-community-8.0.26-1.el7.ppc64le.bin.tar.gz ./8.0.26
