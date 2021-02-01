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
    openssl-devel lz4-devel libxml2-devel wget readline-devel \
    libevent libevent-devel jemalloc-devel libaio-devel git bison cmake libtirpc-devel numad \
    rpm-build time perl-JSON perl-Env

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


### Default %{optflags} is not optimized.
# rpm -Uvh mysql-community-5.7.28-1.el7.src.rpm
# cd /root/rpmbuild/SPECS
# sed -i '/WITH_SSL=system/d' mysql.spec 
# rpmbuild -bb mysql.spec --define='optflags -O3 -mcpu=native -mtune=native -mcmodel=large' 2>&1 | tee build.log


