#!/usr/bin/env bash    #!/shebang标记（指定使用的解释器路径）env程序在PATH中找到bash并执行。bash路径不同系统路径不同，用env避免这个问题，直接用PATH找

echo "====> Download Genomics related tools <===="

mkdir -p $HOME/bin    #主目录下创建bin，如存在，-p不报错直接跳过；$HOME不存在，创建再创建bin。mkdir命令（make directory，创建目录/文件夹），-p（父目录不存在时，创建父目录）
mkdir -p $HOME/.local/bin 
mkdir -p $HOME/share
mkdir -p $HOME/Scripts

# make sure $HOME/bin in your $PATH
if grep -q -i homebin $HOME/.bashrc; then  #grep搜索命令（在文件文本中查找特定字符串），-q（quiet模式，找到匹配内容不输出只返回退出状态，0找到匹配），-i（ignore case忽略大小写）
    echo "==> .bashrc already contains homebin"
else
    echo "==> Update .bashrc"  #.bashrc配置文件，用于自定义配置bash的行为和环境，定义了每次打开终端的一些默认行为

    HOME_PATH='export PATH="$HOME/bin:$HOME/.local/bin:$PATH"'  #定义变量HOME_PATH，值为后面一串字符串。export PATH="..."把...添加到PATH环境变量中，在当前shell和子进程中均生效，不用export定义的话只在当前shell生效。$PATH原来的PATH变量保留在最后，不丢失原来系统环境
    echo '# Homebin' >> $HOME/.bashrc  #>>追加内容
    echo $HOME_PATH >> $HOME/.bashrc  #把刚才定义的HOME_PATH变量追加到.bashrc中
    echo >> $HOME/.bashrc  #输出空行追加文件中

    eval $HOME_PATH  #执行存储在$HOME_PATH变量中的命令。eval命令（将后面的参数作为命令执行，解析并执行它接收到的字符串并作为命令执行），变量的内容可以动态执行（多个命令/命令见条件逻辑/表达式复杂）
fi

# Clone or pull other repos
for OP in dotfiles; do  #定义for循环（遍历一个列表，并将每个元素赋值给OP，这里指dotfiles），用for更有扩展性，可能会有多可仓库不止dotfiles。OP循环变量（每次循环时从列表中获取的元素），OP可任意命名。
    if [[ ! -d "$HOME/Scripts/$OP/.git" ]]; then  #检查$HOME/Scripts/$OP路径下是否存在.git目录，是否有git仓库。-d判断是否存在。！表示取反。如果没有.git目录。$OP循环的元素，这里dotfiles
        if [[ ! -d "$HOME/Scripts/$OP" ]]; then  #检查$HOME/Scripts/下是否存在dotfiles目录
            echo "==> Clone $OP"
            git clone https://github.com/wang-q/${OP}.git "$HOME/Scripts/$OP"
        else
            echo "==> $OP exists"
        fi
    else
        echo "==> Pull $OP"
        pushd "$HOME/Scripts/$OP" > /dev/null  #pushd命令（将当前工作目录保存到“目录堆栈”并切换到目标目录，临时切换目录）。>重定向到/dev/null，丢弃它的输出（不显示在终端），脚本执行更干净。
        git pull
        popd > /dev/null  #popd命令（pushd的反操作，从目录堆栈中弹出之前保存的目录并切换回原来目录），回到执行pushd前的目录
    fi
done  #结束for循环

# alignDB
# chmod +x $HOME/Scripts/alignDB/alignDB.pl  #chmod命令（change mod，改变文件或目录权限）。+x参数（x执行权限，+x给文件添加执行权限，让文件像程序一样可执行）。 .pl表示perl脚本
# ln -fs $HOME/Scripts/alignDB/alignDB.pl $HOME/bin/alignDB.pl  #ln命令（创建链接），-f参数（强制删除目标文件），-s参数（创建符号链接）。前一个源文件，后一个目标位置

echo "==> Jim Kent bin"
cd $HOME/bin/
RELEASE=$( ( lsb_release -ds || cat /etc/*release || uname -om ) 2>/dev/null | head -n1 )
if [[ $(uname) == 'Darwin' ]]; then
    curl -L https://github.com/wang-q/ubuntu/releases/download/20190906/jkbin-egaz-darwin-2011.tar.gz
else
    if echo ${RELEASE} | grep CentOS > /dev/null ; then
        curl -L https://github.com/wang-q/ubuntu/releases/download/20190906/jkbin-egaz-centos-7-2011.tar.gz
    else
        curl -L https://github.com/wang-q/ubuntu/releases/download/20190906/jkbin-egaz-ubuntu-1404-2011.tar.gz
    fi
fi \
    > jkbin.tar.gz

echo "==> untar from jkbin.tar.gz"
tar xvfz jkbin.tar.gz x86_64/axtChain
tar xvfz jkbin.tar.gz x86_64/axtSort
tar xvfz jkbin.tar.gz x86_64/axtToMaf
tar xvfz jkbin.tar.gz x86_64/chainAntiRepeat
tar xvfz jkbin.tar.gz x86_64/chainMergeSort
tar xvfz jkbin.tar.gz x86_64/chainNet
tar xvfz jkbin.tar.gz x86_64/chainPreNet
tar xvfz jkbin.tar.gz x86_64/chainSplit
tar xvfz jkbin.tar.gz x86_64/chainStitchId
tar xvfz jkbin.tar.gz x86_64/faToTwoBit
tar xvfz jkbin.tar.gz x86_64/lavToPsl
tar xvfz jkbin.tar.gz x86_64/netChainSubset
tar xvfz jkbin.tar.gz x86_64/netFilter
tar xvfz jkbin.tar.gz x86_64/netSplit
tar xvfz jkbin.tar.gz x86_64/netSyntenic
tar xvfz jkbin.tar.gz x86_64/netToAxt

mv $HOME/bin/x86_64/* $HOME/bin/
rm jkbin.tar.gz

# if [[ $(uname) == 'Darwin' ]]; then
#     curl -L http://hgdownload.soe.ucsc.edu/admin/exe/macOSX.x86_64/faToTwoBit
# else
#     curl -L http://hgdownload.soe.ucsc.edu/admin/exe/linux.x86_64/faToTwoBit
# fi \
#     > faToTwoBit
# mv faToTwoBit $HOME/bin/
# chmod +x $HOME/bin/faToTwoBit
