基于 Debian的 Linux 发行版本都可以使用 apt-get 命令来进行安装：
sudo apt-get install golang
要查看当前系统安装的 Go 语言版本可以使用如下命令：
go version
由于 Go 代码必需保存在 workspace(工作区)中，所以我们必需在 Home 目录(例如 ~/workspace)创建一个workspace 目录并定义 GOPATH 环境变量指向该目录，这个目录将被 Go 工具用于保存和编辑二进制文件。
mkdir ~/workspace
echo 'export GOPATH="$HOME/workspace"' >> ~/.bashrc
source ~/.bashrc
根据不同的需要，我们可以使用 apt-get 安装 Go tools：
sudo apt-cache search golang
-----------------------------------------------------------
基于 Red Hat 的 Linux 发行版本都可以使用 yum 命令来进行安装：
sudo yum install golang
要查看当前系统安装的 Go 语言版本可以使用如下命令：
go version
接下来还是在 Home 目录(例如 ~/workspace)创建一个 workspace 目录并定义 GOPATH 环境变量指向该目录，这个目录将被 Go 工具用于保存和编辑二进制文件。
mkdir ~/workspace
echo 'export GOPATH="$HOME/workspace"' >> ~/.bashrc
source ~/.bashrc
根据不同的需要，我们可以使用 yum 安装 Go tools：
yum search golang
-----------------------------------------------------------
由于大家使用的 Linux 源不尽相同，也不见得是最新版本或需要版本的 Go 语言包，所以我们说一下如何手动安装指定版本。
下载 Go 语言文件
64-bit Linux
wget http://www.golangtc.com/static/go/go1.4.2.linux-amd64.tar.gz
32-bit Linux
wget http://www.golangtc.com/static/go/go1.4.2.linux-386.tar.gz
下载地址：http://golangtc.com/download
解压二进制文件到 /usr/local 目录
sudo tar -xzf go1.4.2.linux-xxx.tar.gz -C /usr/local
使用 vi 在环境变量配置文件  /etc/profile 中增加如下内容：
export PATH=$PATH:/usr/local/go/bin
检查 Go 语言版本
go version
定义 GOPATH 环境变量到 workspace 目录
export GOPATH="$HOME/workspace
--------------------------------------------------------------
export GOPATH=$HOME/workspace/go
export PATH=$PATH:${GOPATH//://bin:}/bin
export GOBIN=
GOPATH目录结构
goWorkSpace  // (goWorkSpace为GOPATH目录)
  -- bin  // golang编译可执行文件存放路径，可自动生成。
  -- pkg  // golang编译的.a中间文件存放路径，可自动生成。
  -- src  // 源码路径。按照golang默认约定，go run，go install等命令的当前工作路径（即在此路径下执行上述命令）。
go目录结构1:
project1 // (project1添加到GOPATH目录了)
  -- bin
  -- pkg
  -- src  
     -- models       // package
     -- controllers  // package
     -- main.go      // package main〔注意，本文所有main.go均指包main的入口函数main所在文件〕
 project2 // (project2添加到GOPATH目录了)
      -- bin
      -- pkg
      -- src
         -- models       // package
         -- controllers  // package
         -- main.go      // package main
package main文件直接在GOPATH目录到src下。