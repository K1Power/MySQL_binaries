Build on K1 Power9 Linux, RedHat 7.6 (Kernel 4.14.0-115.8.1.el7a.ppc64le) with advance-toolchain-at11.0

### 1. About Build ENV #########################################################################################

# lscpu |grep -i arch
Architecture:          ppc64le
Model name:            POWER9 (architected), altivec supported

# uname -r
4.14.0-115.8.1.el7a.ppc64le

# uname -m
ppc64le

### 2. Build RPMS for mysql#####################################################################################

Install dependencies
# yum -y install zlib-devel bzip2-devel numactl-devel \
       openssl-devel lz4-devel libxml2-devel wget readline-devel ncurses-devel pkgconfig \
       libevent libevent-devel jemalloc-devel libaio-devel git bison doxygen cmake cmake3 \
       libtirpc libtirpc-devel numad libzstd libzstd-devel libedit-devel libicu libicu-devel \
       protobuf protobuf-devel rpm-build time perl-JSON perl-Env 

nstall advance-toolchain-at11.0
# yum install advance-toolchain-at11.0*
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
CFLAGS="-O3 -mcpu=native -mtune=native -mcmodel=large" \
CXXFLAGS="-O3 -mcpu=native -mtune=native -mcmodel=large" \
CPPFLAGS=$CCXXFLAGS  \
LDFLAGS='-ljemalloc ' \
./config --prefix=/opt/openssl
make -j8 VERBOSE=1 && make install

export PATH=/opt/openssl/bin:$PATH
export LD_LIBRARY_PATH=/opt/openssl/lib:$LD_LIBRARY_PATH



# ## Default %{optflags} is not optimized.
# rpm -Uvh mysql-community-8.0.18-1.el7.src.rpm
# ## Some modules like 'mysys/my_error.cc' will raise such error: cc1plus: all warnings being treated as errors,
# ## when compile with '-Werror', so use '-Wall' to show all errors only instead of '-Werror'
# cd /root/rpmbuild/SOURCES
# tar zxf mysql-8.0.18.tar.gz
# sed -i 's/-Werror/-Wall/g' mysql-8.0.18/cmake/maintainer.cmake
# gzip -d mysql-8.0.18.tar.gz
# tar uvf mysql-8.0.18.tar mysql-8.0.18/cmake/maintainer.cmake
# gzip mysql-8.0.18.tar
# ##
# cd /root/rpmbuild/SPECS
### Remove WITH_SSL for compat libs
# sed -i '/WITH_SSL=system/d' mysql.spec 
### In MySQL CMakeList.txt, we can find it will search new gcc if CMAKE_C_COMPILER is not specified.
# sed -i 's/-DCMAKE_C_FLAGS=/-DCMAKE_C_COMPILER=`which gcc` -DCMAKE_C_FLAGS=/g'  mysql.spec
# sed -i 's/-DCMAKE_CXX_FLAGS=/-DCMAKE_CXX_COMPILER=`which g++` -DCMAKE_CXX_FLAGS=/g'  mysql.spec
### Add WITH_SSL for debug and release build, append to WITH_NUMA=ON
# sed -i 's/-DWITH_NUMA=ON/-DWITH_NUMA=ON -DWITH_SSL=\/opt\/openssl/g'  mysql.spec
# rpmbuild -vv -bb mysql.spec --define='optflags -O3 -mcpu=native -mtune=native -mcmodel=large' 2>&1 | tee build.log

