���� Debian�� Linux ���а汾������ʹ�� apt-get ���������а�װ��
sudo apt-get install golang
Ҫ�鿴��ǰϵͳ��װ�� Go ���԰汾����ʹ���������
go version
���� Go ������豣���� workspace(������)�У��������Ǳ����� Home Ŀ¼(���� ~/workspace)����һ��workspace Ŀ¼������ GOPATH ��������ָ���Ŀ¼�����Ŀ¼���� Go �������ڱ���ͱ༭�������ļ���
mkdir ~/workspace
echo 'export GOPATH="$HOME/workspace"' >> ~/.bashrc
source ~/.bashrc
���ݲ�ͬ����Ҫ�����ǿ���ʹ�� apt-get ��װ Go tools��
sudo apt-cache search golang
-----------------------------------------------------------
���� Red Hat �� Linux ���а汾������ʹ�� yum ���������а�װ��
sudo yum install golang
Ҫ�鿴��ǰϵͳ��װ�� Go ���԰汾����ʹ���������
go version
������������ Home Ŀ¼(���� ~/workspace)����һ�� workspace Ŀ¼������ GOPATH ��������ָ���Ŀ¼�����Ŀ¼���� Go �������ڱ���ͱ༭�������ļ���
mkdir ~/workspace
echo 'export GOPATH="$HOME/workspace"' >> ~/.bashrc
source ~/.bashrc
���ݲ�ͬ����Ҫ�����ǿ���ʹ�� yum ��װ Go tools��
yum search golang
-----------------------------------------------------------
���ڴ��ʹ�õ� Linux Դ������ͬ��Ҳ�����������°汾����Ҫ�汾�� Go ���԰�����������˵һ������ֶ���װָ���汾��
���� Go �����ļ�
64-bit Linux
wget http://www.golangtc.com/static/go/go1.4.2.linux-amd64.tar.gz
32-bit Linux
wget http://www.golangtc.com/static/go/go1.4.2.linux-386.tar.gz
���ص�ַ��http://golangtc.com/download
��ѹ�������ļ��� /usr/local Ŀ¼
sudo tar -xzf go1.4.2.linux-xxx.tar.gz -C /usr/local
ʹ�� vi �ڻ������������ļ�  /etc/profile �������������ݣ�
export PATH=$PATH:/usr/local/go/bin
��� Go ���԰汾
go version
���� GOPATH ���������� workspace Ŀ¼
export GOPATH="$HOME/workspace
--------------------------------------------------------------
export GOPATH=$HOME/workspace/go
export PATH=$PATH:${GOPATH//://bin:}/bin
export GOBIN=
GOPATHĿ¼�ṹ
goWorkSpace  // (goWorkSpaceΪGOPATHĿ¼)
  -- bin  // golang�����ִ���ļ����·�������Զ����ɡ�
  -- pkg  // golang�����.a�м��ļ����·�������Զ����ɡ�
  -- src  // Դ��·��������golangĬ��Լ����go run��go install������ĵ�ǰ����·�������ڴ�·����ִ�����������
goĿ¼�ṹ1:
project1 // (project1��ӵ�GOPATHĿ¼��)
  -- bin
  -- pkg
  -- src  
     -- models       // package
     -- controllers  // package
     -- main.go      // package main��ע�⣬��������main.go��ָ��main����ں���main�����ļ���
 project2 // (project2��ӵ�GOPATHĿ¼��)
      -- bin
      -- pkg
      -- src
         -- models       // package
         -- controllers  // package
         -- main.go      // package main
package main�ļ�ֱ����GOPATHĿ¼��src�¡�