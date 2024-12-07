#!/usr/bin/env bash

echo "====> Install softwares via apt-get <===="

echo "==> disabling the release upgrader"
sudo sed -i.bak 's/^Prompt=.*$Prompt=never/' /etc/update-manager/release-upgrades   #修改系统文件并替换其中一行内容
#sudo（superuser do管理员权限）。sed（stream editor对文件或输入流进行批量文本处理），后输出处理后的文本，源文件内容不变。
#-i（in-place editing就地编辑，直接修改文件内容）。.bak（-i的扩展，修改文件前保存副本后缀.bak）。
#'...'sed的正则表达式。s/模式/替换文本/。^行的开头（Prompt=开头的行），

# Virtual machines needn't this and I want life easier    #虚拟机不需要apparmor，在vagrant创建的虚拟机系统中禁用apparmor
# https://help.ubuntu.com/lst/serverguide/apparmor.html    
#apparmor（application armor）linux系统的安全模块，提供强制访问控制机制，限制访问权限，提高安全性。但可能会干扰程序运行，禁用后更自由
if [ "$(whoami)" == 'vagrant'];then    #whoami命令（返回当前用户的用户名），vagrant环境中默认用户名vagrant。
    echo "==> Disable AppArmor"     #vagrant创建和管理虚拟机，不同环境开发测试，提供一个一致、可移植、隔离的开发环境，不受硬件等影响
    sudo service apparmor stop
    sudo update-rc.d -f apparmor remove    #update-rc.d用于启动或禁用服务的自动启动（与系统初始化相关）。-f（force）。remove删除服务的启动脚本
fi

echo "==> Disable whoopsie"   
#禁用ubuntu中whoopsie服务，防止系统崩溃时自动报告错误至ubuntu社区。禁用防止隐私数据外泄、消耗宽带、占用资源
sudo sed -i 's/report_crashes=true/report_crashes=false/' /etc/default/whoopsie   #true启用，false禁用。但whoopsie仍可能后台运行
sudo service whoopsie stop   #service命令（即时管理系统服务的运行状态）。whoopsie（崩溃报告服务）。stop停止whoopsie服务后台运行

echo "==> Install linuxbrew dependences"
sudo apt-get -y update   #apt-get（advanced package tool）管理软件包的命令行工具，-y（yes，自动回答所有提示为yes，无需手动确认）
sudo apt-get -y upgrade
sudo apt-get -y install build-essential curl file git
sudo apt-get -y install pkg-config libbz2-dev zlib1g-dev liblzma-dev libzstd-dev libexpat1-dev  #各种压缩解压的开发包
# sudo apt-get -y install libcurl4-openssl-dev libncurses-dev
 
echo "==> Install other software"
sudo apt-get -y install aptitude net-tools parallel vim screen xlstproc numactl mlocate    
#aptitude前端工具，net-tools网络相关工具的包，parallel并行执行任务工具，vim高级文本编辑器，screen终端多路复用器，xsltpro/xslt转xml
#numactl控制unma系统的工具，mlocate查找文件的工具

echo "==> Install develop libraries"
# sudo apt-get -y install libreadline-dev libedit-dev
sudo apt-get -y install libdb-dev libxml2-dev libssl-dev libncurses5-dev libgd-dev
# sudo apt-get -y install gdal-bin gdal-data libgdal-dev # /usr/lib/libgdal.so: undefined reference to `TIFFReadDirectory@LIBTIFF_4.0'
# sudo apt-get -y install libgsl0ldbl libgsl0-dev

# Gtk stuff, Need by alignDB
# install them in a fresh machine to avoid problems
echo "==> Install gtk3"
#sudo apt-get -y install libcairo2-dev libglib2.0-0 libglib2.0-dev libgtk-3-dev libgirepository1.0-dev
#sudo apt-get -y install gir1.2-glib-2.0 gir1.2-gtk-3.0 gir1.2-webkit-3.0

echo "==> Install gtk3 related tools"
# sudo apt-get -y install xvfb glade

echo "==> Install graphics tools"
sudo apt-get -y install gnuplot graphviz imagemagick

#echo "==> Install nautilus plugins"
#sudo apt-get -y install nautilus-open-terminal nautilus-actions

# Mysql will be installed separately.
# Remove system provided mysql package to avoid confusing linuxbrew.
echo "==> Remove system provided mysql"
# sudo apt-get -y purge mysql-common

echo "====> Basic software installation complete! <===="
