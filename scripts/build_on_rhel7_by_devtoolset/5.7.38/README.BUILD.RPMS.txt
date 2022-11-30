Build on K1 Power9 Linux, CentOS 7.9 (Kernel 4.18.0-193.28.1.el7.ppc64le) with devtoolset-9.

### 1. About Build ENV #########################################################################################

# lscpu |grep -i arch
Architecture:          ppc64le
Model name:            POWER9 (architected), altivec supported

# uname -r
4.18.0-193.28.1.el7.ppc64le

# uname -m
ppc64le

### 2. Build mysql    ##########################################################################################

Install dependencies
# yum -y install zlib-devel bzip2-devel numactl-devel \
    openssl-devel lz4-devel libxml2-devel wget readline-devel \
    libevent-devel jemalloc-devel libaio-devel git bison cmake libtirpc-devel numad \
    install java-1.8.0-openjdk iotop dstat perf java-1.8.0-openjdk-devel nmon \
    rpm-build time perl-JSON perl-Env  cyrus-sasl-devel openldap-devel

Install devtoolset-9
# yum install devtoolset-9
source /opt/rh/devtoolset-9/enable
# type gcc
gcc is /opt/rh/devtoolset-9/root/usr/bin/gcc
# gcc --version 
gcc (GCC) 9.3.1 20200408 (Red Hat 9.3.1-2)

### Default %{optflags} is not optimized.
# rpm -Uvh mysql-community-5.7.38-1.el7.src.rpm
# cd /root/rpmbuild/SPECS
# rpmbuild -bb mysql.spec --define='optflags -O3 -mcpu=native -mtune=native -mcmodel=large' 2>&1 | tee build.log

