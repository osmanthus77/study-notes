#!/usr/bin/env bash

echo "====> Building Genomics related tools <===="

echo "==> Anchr"
# TODO: superreads can't find `jellyfish/circular_buffer.hpp`   #`...`头文件路径
#    `include/jellyfish-2.2.4`    #可能解决思路，检查`...`这个目录

#curl -fsSL https://raw.githubusercontent.com/wang-q/App-Anchr/master/share/install_dep.sh | bash
#-f（fail silently on server errors，如果返回错误停止运行并返回错误码） -s（silent静默模式，隐藏进度和错误信息，只返回结果/标准输出）
#-S（show errors when -s is uesd搭配-s使用，显示错误信息，避免silent隐藏错误导致问题难以排查）  -L（follow redirects自动跟随重定向）

echo "==> Install bioinformatics softwares"
brew tap brewsci/bio   #基于homebrew的管理生信工具的软件包库，homebrew社区中安装生信工具的tap扩展源
brew tap brewsci/science  #homebrew另一tap扩展源，用于安装计算统计等，已弃用

brew install clustal-w mafft muscle trimal    #多序列比对工具，trimal比对结果修剪
brew install lastz diamond paml fasttree iqtree # raxml
brew install raxml --without-open-mpi
brew install --force-bottle newick-utils
brew install fastqc # kat
brew install bowtie bowtie2 bwa
brew install samtools picard-tools
brew install stringtie hisat2 # tophat cufflinks
brew install seqtk minimap2 minigraph #gfatools
brew install genometools # igvtools
brew install --build-from-source snp-sites #macOS bottles broken
brew install bcftools
