#!/bin/bash

export HOMEBREW_NO_AUTO_UPDATE=1  #设置环境变量HOMEBREW_NO_AUTO_UPDATE，值为1，启用禁止自动更新的的功能
# export ALL_PROXY=socks5h://localhost:1080  #环境变量ALL_PROXY（告诉程序在访问网络时需要使用代理）。socks5h://localhost:1080（指定的代理地址），让某个程序通过本机上的SOCKS5代理访问互联网。
#socks5h（使用SOCKS5代理协议，h表示由代理服务器解析DNS，否则客户端解析）。localhost表示代理服务在本地计算机运行。1080表示SOCKS5代理的端口

# Clear caches  #清除缓存
rm -f $(brew --cache)/*.incomplete  #rm删除命令（文件/目录）。-f参数（强制删除）。brew --cache命令（返回homebrew缓存目录的路径）。/*.incomplete（homebrew缓存目录中，所有以.incomplete结尾的文件，网络中断/安装失败的临时文件）

echo "==> gcc"
RELEASE=$( ( lsb_release -ds || cat /etc/*release || uname -om ) 2>/dev/null | head -n1 )   #-d参数显示描述信息（系统名称和版本），-s参数输出简介字符串。test命令中-d表示检查指定路径是否是一个目录
if [[ $(uname) == 'Darwin' ]]; then
    brew install pkg-config  #pkg-config管理和查询库文件的工具，正确查找需要的外部库的路径和编译标志
else
    if echo ${RELEASE} | grep CentOS > /dev/null ; then
        brew install gcc   #gcc（GNU Compiler Collection）编译器
        brew install pkg-config
        brew unlink pkg-config  #brew unlink命令（断开当前链接的软件包），解除与当前版本pkg-config链接，可能避免与其他版本冲突，或切换到不同版本
    else
        brew install gcc
        brew install pkg-config
        brew unlink pkg-config
    fi
fi

# perl
echo "==> Install Perl 5.34"
brew install perl

if grep -q -i PERL_534_PATH $HOME/.bashrc; then
    echo "==> .bashrc already contains PERL_534_PATH"
else
    echo "==> Updating .bashrc with PERL_534_PATH..."
    PERL_534_BREW=$(brew --prefix)/Cellar/$(brew list --versions perl | sed 's/ /\//' | head -n 1)  #brew list --versions perl（列出通过brew安装的perl的版本信息）
    #sed 's/ /\//'中，sed流编辑器（处理文本），sed 's/ /\//'表示将输出内容中的第一个空格替换成/，从而构造类似路径的字符串，符合homebrew cellar目录结构的格式。最终可能是/usr/local/Cellar/perl/5.34.0这个路径
    PERL_534_PATH="export PATH=\"$PERL_534_BREW/bin:\$PATH\""   #反斜杠\表示对后面的字符进行转义处理
    #第一个\"转义双引号，保持"字面意义并让它出现在字符串内部。第二个\$转义美元符号，让$PATH作为文本字符串被处理，而不被当成环境变量的值展开。第三个\"同第一个。
    echo '# PERL_534_PATH' >> $HOME/.bashrc
    echo $PERL_534_PATH    >> $HOME/.bashrc
    echo >> $HOME/.bashrc

    # make the above environment variables available for the rest of this script
    eval $PERL_534_PATH
fi

hash cpanm 2>/dev/null || {   #hash命令（查找命令的位置并将其缓存，提高效率；也可以检查命令是否已经缓存）。cpanm（cpanminus命令行工具的缩写）。2>/dev/null ||标准错误输出重定向丢弃，如cpanm没安装也不输出错误信息
    curl -L https://cpanmin.us |  #判断没有cpanm后用这两行命令下载安装cpanm
        perl - -v --mirror-only --mirror http://mirrors.ustc.edu.cn/CPAN/ App::cpanminus  #perl表示用perl执行脚本，-表示告诉perl从标准输入读取脚本（上面下载的）。
        #-v（verbose详细信息）。--mirror-only长选项（cpanm特有，表示仅使用镜像源）。--mirror 指定后面链接为镜像源。App::cpanminus是cpanm工具核心模块，安装后可在系统上用cpanm安装perl模块
}

# Some building tools
echo "==> Building tools"
brew install autoconf libtool automake # autogen         #autoconf工具（生成configure脚本——自动化的结构脚本，检查配置环境）。libtool工具（自动化管理共享库dynamic library）。automake工具（基于makefile.am自动生成makefile.in文件）
brew install cmake   #cmake开源的跨平台自动化构建系统
brew install bison flex   #bison语法分析器生成器，根据定义的语法规则自动生成解析器代码。flex词法分析器生成器，生成用于扫描文本的程序。flex生成词法分析器，将输入流拆分成记号，传递给bison生成的语法分析器，解析源代码的结构。

# libs
brew install gd gsl jemalloc boost # fftw     
#gd创建和处理图像的库（通过变成生成图像并进行操作），gsl科学计算库（数学和统计学函数计算），jemalloc内存分配器，boost（c++库的集合，扩展标准库功能）。fftw快速傅立叶变换的库（信号处理、频域分析）
brew install libffi libgit2 libxml2 libgcrypt libxslt
#libffi库（提供外部函数接口，调用其他语言编写的代码），libgit2库（c语言编写的库，git版本控制功能，嵌入git操作），libxml2库（处理xml可扩展标记语言，解析数据创建或修改xml文档），libgcrypt库（加密库，用于加密解密等），libxslt（xslt可扩展样式表语言转换的处理库，xml转其他格式）
brew install pcre libedit readline sqlite nasm yasm
#pcre库（与perl兼容的库，可使用类似perl正则表达式语法），libedit（命令行编辑库），readline（命令行输入编辑库），sqlite（关系型数据库管理系统），nasm（汇编语言编译器，源代码编译成机器代码），yasm（类似nasm，兼容多种语法，可跨平台编译）
brew install bzip2 gzip libarchive libzip xz
#bzip2文件压缩工具（.bz2格式），gzip文件压缩（.gz格式），libarchive库（读取和写入归档文件），libzip库（处理zip文件），xz文件压缩工具（.xz格式）
# brew link --force libffi   #将libffi库安装路径中的文件（包括共享库、头文件等）强制链接到系统的标准路径中（通常是 /usr/local/include 和 /usr/local/lib）

# python
brew install python@3.9

if grep -q -i PYTHON_39_PATH $HOME/.bashrc; then
    echo "==> .bashrc already contains PYTHON_39_PATH"
else
    echo "==> Updating .bashrc with PYTHON_39_PATH..."
    PYTHON_39_PATH="export PATH=\"$(brew --prefix)/opt/python@3.9/bin:$(brew --prefix)/opt/python@3.9/libexec/bin:\$PATH\""
    echo '# PYTHON_39_PATH' >> $HOME/.bashrc
    echo ${PYTHON_39_PATH} >> $HOME/.bashrc
    echo >> $HOME/.bashrc

    # make the above environment variables available for the rest of this script
    eval ${PYTHON_39_PATH}
fi

# https://github.com/Homebrew/homebrew-core/issues/43867
# Upgrading pip breaks pip
#pip3 install --upgrade pip setuptools wheel

# r
# brew install wang-q/tap/r@3.6.1
hash R 2>/dev/null || {
    echo "==> Install R"
    brew install r
}

cpanm --mirror-only --mirror http://mirrors.ustc.edu.cn/CPAN/ --notest Statistics::R

# java
echo "==> Install Java"
if [[ "$OSTYPE" == "darwin"* ]]; then
    brew install openjdk
else
    brew install openjdk
    # brew link openjdk --force
fi
brew install ant maven

# pin these
# brew pin perl
# brew pin python@3.9
# brew pin r

# other programming languages
brew install lua node

# taps
brew tap wang-q/tap

# downloading tools
brew install aria2 curl wget

# gnu
brew install gnu-sed gnu-tar

# other tools
brew install screen stow htop parallel pigz
brew install tree pv
brew install jq jid pup
brew install datamash miller tsv-utils
brew install proxychains-ng

brew install bat tealdeer # exa tiv
brew install hyperfine ripgrep tokei
brew install bottom # zellij

# large packages
if [[ "$OSTYPE" == "darwin"* ]]; then
    brew install gpg2
fi

hash pandoc 2>/dev/null || {
    brew install pandoc
}

hash gnuplot 2>/dev/null || {
    brew install gnuplot
}

hash dot 2>/dev/null || {
    brew install graphviz
}

hash convert 2>/dev/null || {
    brew install imagemagick
}

hash rsvg-converter 2>/dev/null || {
    brew install librsvg
}

hash udunits2 2>/dev/null || {
    brew install udunits
}

# weird dependancies by Cairo.pm
# brew install linuxbrew/xorg/libpthread-stubs linuxbrew/xorg/renderproto linuxbrew/xorg/kbproto linuxbrew/xorg/xextproto

# gtk+3
# brew install gsettings-desktop-schemas gtk+3 adwaita-icon-theme gobject-introspection
