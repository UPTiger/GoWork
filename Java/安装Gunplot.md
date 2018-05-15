安装Erlang



### 首先下载资源
wget http://erlang.org/download/otp_src_18.3.tar.gz

### 解压
tar -xzvf　otp_src_18.3.tar.gz

### 安装依赖包
yum install -y gcc gcc-c++ unixODBC-devel openssl-devel ncurses-devel

### 设定安装位置
./configure --prefix=/usr/local/erlang --without-javac

### 安装
make && make install

### 添加环境变量
vi /etc/profile

export PATH=$PATH:/usr/local/erlang-without-javac/bin

------------------------------------------------------------------------------------------

安装Gunplot

cd /usr/local/src

wget https://jaist.dl.sourceforge.net/project/gnuplot/gnuplot/5.2.2/gnuplot-5.2.2.tar.gz

tar -zxvf gunplot-5.2.2.tar.gz

```
cd /usr/local/src/
wget http://sourceforge.net/projects/gnuplot/files/gnuplot/4.4.2/gnuplot-4.4.2.tar.gz/download
tar xzf gnuplot-4.4.2.tar.gz 
cd gnuplot-4.4.2
less INSTALL

# start compiling. I usually install self-compiled stuff at /opt/[PKG-NAME]
./configure --prefix=/opt/gnuplot442
make
# make sure the shipped version of gnuplot is removed (this is probably not necessary but prevents version mix-up)
yum remove gnuplot
make install

# you might want to add a symlink
ln -s /opt/gnuplot442/bin/gnuplot /usr/bin/gnuplot
```

安装TSung

下载

wget http://tsung.erlang-projects.org/dist/ http://tsung.erlang-projects.org/dist/tsung-1.7.0.tar.gz

tar -zvxf tsung-1.7.0.tar.gz 

cd tsung-1.7.0

 ./configure -prefix=/usr/local/tsung

make

./configure --prefix=/usr/local/tsung

 make && make install

 export PATH=$PATH:/usr/local/tsung/bin/



安装Template

wget https://www.cpan.org/modules/by-module/Template/Template-Toolkit-2.26.tar.gz

 tar -zxf Template-Toolkit-2.26.tar.gz 

 cd Template-Toolkit-2.26

perl Makefile.PL 

make

make test

make install





