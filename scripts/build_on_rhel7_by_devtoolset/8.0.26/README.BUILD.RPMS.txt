Build on K1 Power9 Linux, RedHat 7.6 (Kernel 4.14.0-115.8.1.el7a.ppc64le) with devtoolset-9.

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

Install devtoolset-9
# yum install devtoolset-9
source /opt/rh/devtoolset-9/enable
# type gcc
gcc is /opt/rh/devtoolset-9/root/usr/bin/gcc
# gcc --version 
gcc (GCC) 7.3.1 20180303 (Red Hat 7.3.1-5)

# ## Default %{optflags} is not optimized.
# rpm -Uvh mysql-community-8.0.21-1.el7.src.rpm
# ## Some modules like 'mysys/my_error.cc' will raise such error: cc1plus: all warnings being treated as errors,
# ## when compile with '-Werror', so use '-Wall' to show all errors only instead of '-Werror'
# cd /root/rpmbuild/SOURCES
# tar zxf mysql-8.0.21.tar.gz
# sed -i 's/-Werror/-Wall/g' mysql-8.0.21/cmake/maintainer.cmake
# gzip -d mysql-8.0.21.tar.gz
# tar uvf mysql-8.0.21.tar mysql-8.0.21/cmake/maintainer.cmake
# gzip mysql-8.0.21.tar
# ##
# cd /root/rpmbuild/SPECS
# rpmbuild -vv -bb mysql.spec --define='optflags -O3 -mcpu=native -mtune=native -mcmodel=large' 2>&1 | tee build.log

