Layered Building  
====
  
reference  
https://review.opendev.org/#/c/721517/12/doc/source/developer_resources/build_guide.rst  
https://docs.starlingx.io/developer_resources/Layered_Build.html  
  
  
Step.0 检查基本条件 下载安装工具 准备构建环境  
  0.a) 更新os  
  0.b) 创建使用者  
  0.c) 安装依赖  
  
Step.1 构建"基础构建镜像"  
  1.d) 下载starlingx/tool 并用此安装  
  
Step.2 运行"构建环境镜像" 容器 并下载镜像仓库  
  2.e) 使用starlingx/tool/tb.sh 启动容器  
  2.f) 使用repo批量下载flock/distro/compiler源码  
  3.g) 下载CentOS镜像  
  
Step.3 构建Starlingx安装包  
  3.)  
  
  
sudo apt-get update  
sudo apt-get install make git curl  
sudo apt-get remove docker docker-engine docker.io  
sudo apt-get install apt-transport-https ca-certificates curl gnupg2 software-properties-common  
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -  
sudo add-apt-repository \  
   "deb [arch=amd64] https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/ubuntu \  
   $(lsb_release -cs) \  
   stable"  
sudo apt-get update  
sudo apt-get install docker-ce  
sudo usermod -aG docker yuchengde  
docker login  
  
cd $HOME  
git clone https://opendev.org/starlingx/tools.git  
cd $HOME/tools/  
vim localrc  
       # tbuilder localrc  
       MYUNAME=yuchengde  
       PROJECT=starlingx  
       HOST_PREFIX=$HOME/starlingx/workspace  
       HOST_MIRROR_DIR=$HOME/starlingx/mirror  
bash tb.sh create  
bash tb.sh env  
bash tb.sh run  
bash tb.sh exec  
  
eval $(ssh-agent)  
ssh-add  
  
cd $MY_REPO_ROOT_DIR  
export REPO_URL='https://mirrors.tuna.tsinghua.edu.cn/git/git-repo'  

repo init -u https://opendev.org/starlingx/manifest.git -b master -m compiler.xml  
repo sync  
export LAYER=compiler  
cd  $MY_REPO_ROOT_DIR  
echo “LAYER=$LAYER” >> stx-tools/localrc  
cd  $MY_REPO_ROOT_DIR/stx-tools/centos-mirror-tools  
sudo bash download_mirror.sh  
exit  
cd $HOME/starlingx/mirror/CentOS/  
cp -r $HOME/starlingx/workspace/localdisk/designer/<user>/<project>/stx-tools/centos-mirror-tools/output/stx-r1 .  
cd $HOME/tools/  
bash tb.sh exec  
ln -s /import/mirrors/CentOS/stx-r1/CentOS/downloads/ $MY_REPO/stx/  
populate_downloads.sh /import/mirrors/CentOS/stx-r1/CentOS/  
generate-cgcs-centos-repo.sh /import/mirrors/CentOS/stx-r1/CentOS/  
build-pkgs  
build-pkgs --installer  

repo init -u https://opendev.org/starlingx/manifest.git -b master -m distro.xml  
repo sync  
export LAYER=distro  
cd  $MY_REPO_ROOT_DIR  
echo “LAYER=$LAYER” >> stx-tools/localrc  
cd  $MY_REPO_ROOT_DIR/stx-tools/centos-mirror-tools  
sudo bash download_mirror.sh  
ln -s /import/mirrors/CentOS/stx-r1/CentOS/downloads/ $MY_REPO/stx/  
populate_downloads.sh /import/mirrors/CentOS/stx-r1/CentOS/  
generate-cgcs-centos-repo.sh /import/mirrors/CentOS/stx-r1/CentOS/  
build-pkgs  
build-pkgs --installer  

repo init -u https://opendev.org/starlingx/manifest.git -b master -m flock.xml  
repo sync  
export LAYER=flock  
cd  $MY_REPO_ROOT_DIR  
echo “LAYER=$LAYER” >> stx-tools/localrc  
cd  $MY_REPO_ROOT_DIR/stx-tools/centos-mirror-tools  
sudo bash download_mirror.sh -c ./yum.conf.sample -n -g  
ln -s /import/mirrors/CentOS/stx-r1/CentOS/downloads/ $MY_REPO/stx/  
populate_downloads.sh /import/mirrors/CentOS/stx-r1/CentOS/  
generate-cgcs-centos-repo.sh /import/mirrors/CentOS/stx-r1/CentOS/  
build-pkgs  
build-iso  
  
