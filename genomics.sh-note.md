#!/usr/bin/env bash

echo "====> Building Genomics related tools <===="

echo "==> Anchr"
# TODO: superreads can't find `jellyfish/circular_buffer.hpp`   #`...`头文件路径
#    `include/jellyfish-2.2.4`    #可能解决思路，检查`...`这个目录

#curl -fsSL https://raw.githubusercontent.com/wang-q/App-Anchr/master/share/install_dep.sh | bash
#-f（fail silently on server errors，如果返回错误停止运行并返回错误码） -s（silent静默模式，隐藏进度和错误信息，只返回结果/标准输出）
#-S（show errors when -s is uesd搭配-s使用，显示错误信息，避免silent隐藏错误导致问题难以排查）  -L（follow redirects自动跟随重定向）

echo "==> Install bioinformatics softwares"
brew tap brewsci/bio
brew tap brewsci/science

brew install clustal-w mafft muscle trimal