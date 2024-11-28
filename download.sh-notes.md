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
        git pull  #拉取远程仓库的最新更新
        popd > /dev/null  #popd命令（pushd的反操作，从目录堆栈中弹出之前保存的目录并切换回原来目录），回到执行pushd前的目录
    fi
done  #结束for循环

# alignDB
# chmod +x $HOME/Scripts/alignDB/alignDB.pl  #chmod命令（change mod，改变文件或目录权限）。+x参数（x执行权限，+x给文件添加执行权限，让文件像程序一样可执行）。 .pl表示perl脚本
# ln -fs $HOME/Scripts/alignDB/alignDB.pl $HOME/bin/alignDB.pl  #ln命令（创建链接），-f参数（强制删除目标文件），-s参数（创建符号链接）。前一个源文件，后一个目标位置

echo "==> Jim Kent bin"  Jim Kent bin 用于基因组学分析的bin
cd $HOME/bin/   #切换当前目录到/bin。cd命令（change directory，改变目录）。
RELEASE=$( ( lsb_release -ds || cat /etc/*release || uname -om ) 2>/dev/null | head -n1 )  #获取当前系统发行版本/操作系统类型，并将结果保存到RELEASE变量中。
#lsb_release命令（显示linux发行版信息）。-d显示发行版描述信息。-s只显示简洁名称。||逻辑运算符（或），前一命令失败执行后一个。cat命令（查看文件内容，输出到终端or传递给其他命令）。*release通配符，所有以release结尾的文件名。uname命令（显示操作系统信息）。-o显示操作系统名称，-m显示机器的硬件架构（eg.x86_64）
#2>/dev/null重定向输出错误，前面命令出现错误时不显示在终端。2（标准错误输出stderr）>重定向符号，/dev/null特殊的设备文件，丢弃所有输入它的数据。
#head -n1（前面命令成功时传给head -n1，显示管道传入数据的第一行）。head命令（显示文件的前几行，默认前10行）
if [[ $(uname) == 'Darwin' ]]; then  #判断当前系统是否macos。$(uname)当前操作系统名称。[[ ... ]]条件测试。==字符串相等比较。
    curl -L https://github.com/wang-q/ubuntu/releases/download/20190906/jkbin-egaz-darwin-2011.tar.gz
else  #系统非macos的话
    if echo ${RELEASE} | grep CentOS > /dev/null ; then  #${RELEASE}引用变量RELEASE的值（刚才得到的操作系统信息），再用echo输出到grep。grep命令（搜索指定字符串，这里centos）
        curl -L https://github.com/wang-q/ubuntu/releases/download/20190906/jkbin-egaz-centos-7-2011.tar.gz
    else
        curl -L https://github.com/wang-q/ubuntu/releases/download/20190906/jkbin-egaz-ubuntu-1404-2011.tar.gz
    fi
fi \  #fi（if语句结束）。\换行符续行符，当前行的命令未结束。下一句是跟if语句同一命令行的一部分，要继续执行。
    > jkbin.tar.gz  #把上面输出的内容保存到jkbin.tar.gz文件中

echo "==> untar from jkbin.tar.gz"
tar xvfz jkbin.tar.gz x86_64/axtChain  #axtChain基因组比对工具（尤其链比对），axt转chain格式。tar命令（打包和解压文件，处理.tar、.tar.gz、.tgz格式）。xvfz是传递给tar命令的选项，x解压文件extract，v详细模式verbose（解压时显示详细进程，列出正在解压的文件），f指定文件file（f之后的文件是目标解压文件），z通过gzip解压。
tar xvfz jkbin.tar.gz x86_64/axtSort  #axtSort排序axt格式的比对结果
tar xvfz jkbin.tar.gz x86_64/axtToMaf  #axtToMaf将axt格式的基因组比对结果转化为maf格式，axt格式一般两条基因组序列比对结果，maf多序列比对结果
tar xvfz jkbin.tar.gz x86_64/chainAntiRepeat  #chainAntiRepeat处理基因组比对中的重复序列，检测并去除比对中与重复序列相关的比对链
tar xvfz jkbin.tar.gz x86_64/chainMergeSort  #chainMergeSort合并（多个小片段合成大的比对区域）和排序基因组比对链。
tar xvfz jkbin.tar.gz x86_64/chainNet  #chainNet把chain转net格式，基因组比对结果的筛选和转换（质量、覆盖度）
tar xvfz jkbin.tar.gz x86_64/chainPreNet  #chainPreNet把chain转net前的一步，初步筛选转换，预处理去除低质量or冗余的比对数据，为chainnet作准备
tar xvfz jkbin.tar.gz x86_64/chainSplit  #chainSplit分割基因组比对链chain，单个大链拆分多个小链，精确分析范围
tar xvfz jkbin.tar.gz x86_64/chainStitchId  #chainStitchId合并链chain文件中比对数据，多个独立链拼接形成连续的chain
tar xvfz jkbin.tar.gz x86_64/faToTwoBit  #faToTwoBit把fasta格式转2bit二进制格式，显著减少文件大小。A00，T01，C10，G11
tar xvfz jkbin.tar.gz x86_64/lavToPsl  #lavToPsl把lav格式转psl格式，lav存储基因组比对结果，psl信息更详细。不同工具兼容、一致性更好
tar xvfz jkbin.tar.gz x86_64/netChainSubset  #netChainSubset从chain文件提取子集，大的chain文件中提取特定范围的比对数据
tar xvfz jkbin.tar.gz x86_64/netFilter  #netFilter过滤基因组比对数据，从chain文件中筛选符合特定条件的比对记录
tar xvfz jkbin.tar.gz x86_64/netSplit  #netSplit分割链文件，尤其是基因组比对中的net数据
tar xvfz jkbin.tar.gz x86_64/netSyntenic  #netSyntenic筛选和提取同源区段，分析net文件识别并提取基因组见的同源区段，表征保守区域
tar xvfz jkbin.tar.gz x86_64/netToAxt  #netToAxt把net转axt格式，axt更侧重单一的比对，net更高层次比对

mv $HOME/bin/x86_64/* $HOME/bin/  #将$HOME/bin/x86_64/所有文件移动到$HOME/bin/中。mv命令（move，移动文件或目录）。$HOME/bin/存放安装的可执行程序，移动后更容易被执行和调用
rm jkbin.tar.gz  #rm命令（删除）

# if [[ $(uname) == 'Darwin' ]]; then
#     curl -L http://hgdownload.soe.ucsc.edu/admin/exe/macOSX.x86_64/faToTwoBit
# else
#     curl -L http://hgdownload.soe.ucsc.edu/admin/exe/linux.x86_64/faToTwoBit
# fi \
#     > faToTwoBit
# mv faToTwoBit $HOME/bin/
# chmod +x $HOME/bin/faToTwoBit
