Layered Building
reference 
https://review.opendev.org/#/c/721517/12/doc/source/developer_resources/build_guide.rst
https://docs.starlingx.io/developer_resources/Layered_Build.html


Step.0 检查基本条件 下载安装工具 准备构建环境\n 
  0.a) 更新os\n
  0.b) 创建使用者\n
  0.c) 安装依赖\n

Step.1 构建"基础构建镜像"
  1.d) 下载starlingx/tool 并用此安装

Step.2 运行"构建环境镜像" 容器 并下载镜像仓库
  2.e) 使用starlingx/tool/tb.sh 启动容器
  2.f) 使用repo批量下载flock/distro/compiler源码
  3.g) 下载CentOS镜像

Step.3 构建Starlingx安装包
  3.) 


cd $HOME
git clone https://opendev.org/starlingx/tools.git
cd $HOME/tools/
vim localrc
       # tbuilder localrc
       MYUNAME=<your user name>
       PROJECT=<project name>
       HOST_PREFIX=$HOME/starlingx/workspace
       HOST_MIRROR_DIR=$HOME/starlingx/mirror
sudo ./tb.sh create
bash tb.sh env
bash tb.sh run
bash tb.sh exec

eval $(ssh-agent)
ssh-add

cd $MY_REPO_ROOT_DIR
repo init -u https://opendev.org/starlingx/manifest.git -b master -m flock.xml
repo sync
export LAYER=flock
echo “LAYER=$LAYER” >> stx-tools/localrc
cd /stx-tools/centos-mirror-tools
download_mirror.sh -c ./yum.conf.sample -n -g
ln -s /import/mirrors/CentOS/stx-r1/CentOS/downloads/ $MY_REPO/stx/
populate_downloads.sh /import/mirrors/CentOS/stx-r1/CentOS/
generate-cgcs-centos-repo.sh /import/mirrors/CentOS/stx-r1/CentOS/
build-pkgs
build-iso
