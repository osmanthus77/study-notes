#!/usr/bin/env bash

export ENSEMBL_VERSION='105'    #指定ensembl版本号105

cd /tmp   #切换到/tmp（临时文件存储）

if [ ! -e ensembl-${ENSEMBL_VERSION}.tar.gz ]; then    #-e选项（文件测试，检查指定文件是否存在）。目标文件ensembl-105.tar.gz
    echo "==> Get ensembl tarballs"    #tarballs压缩包
    wget https://github.com/Ensembl/ensembl/archive/release/${ENSEMBL_VERSION}.tar.gz           -O ensembl-${ENSEMBL_VERSION}.tar.gz   #-O选项（O大写，指定下载文件保存名称）
    wget https://github.com/Ensembl/ensembl-compara/archive/release/${ENSEMBL_VERSION}.tar.gz   -O ensembl-compara-${ENSEMBL_VERSION}.tar.gz
    wget https://github.com/Ensembl/ensembl-variation/archive/release/${ENSEMBL_VERSION}.tar.gz -O ensembl-variation-${ENSEMBL_VERSION}.tar.gz
fi

SITE_PERL=$(perl -e '($first) = grep {/site_perl/} grep {!/darwin/i and !/x86_64/} @INC; print $first')    #SITE_PERL（指定perl模块的安装路径）。@INC是perl特殊数组，包含了perl查找模块的路径
#-e（在命令行中执行perl代码）。'...'是一个perl脚本。第二个grep过滤器（处理数组中每个元素后返回符合条件的），这里指过滤掉@INC中包含darwin和x86_64的路径。!/darwin/排除掉包含darwin的路径
#第一个grep对第二个grep的结果进一步筛选，/site_perl/匹配包含site_perl的路径。($first) 把两个grep出来的第一个元素赋值给变量$first。{}用于代码块（一段可执行代码）或正则表达式（定义匹配模式/筛选条件）

echo "==> ensembl"
tar xfz /tmp/ensembl-${ENSEMBL_VERSION}.tar.gz \    # x选项（extract解压），f（指定文件），z（用gzip解压）。反斜杠\行连接符（下一行继续命令）
    ensembl-release-${ENSEMBL_VERSION}/modules/Bio/EnsEMBL     #只解压ensembl-${ENSEMBL_VERSION}.tar.gz中这一行这个目录
cp -R ensembl-release-${ENSEMBL_VERSION}/modules/Bio ${SITE_PERL}   #cp命令（复制文件），-R递归复制，复制的路径为/Bio目录下${SITE_PERL}变量定义的路径

echo "==> ensembl-compara"    #ensembl-compara是ensembl子模块，基因组比较
tar xfz /tmp/ensembl-compara-${ENSEMBL_VERSION}.tar.gz \
    ensembl-compara-release-${ENSEMBL_VERSION}/modules/Bio/EnsEMBL
cp -R ensembl-compara-release-${ENSEMBL_VERSION}/modules/Bio ${SITE_PERL}

echo "==> ensembl-variation"  #ensembl-variation，基因组变异
tar xfz /tmp/ensembl-variation-${ENSEMBL_VERSION}.tar.gz \
    ensembl-variation-release-${ENSEMBL_VERSION}/modules/Bio/EnsEMBL
cp -R ensembl-variation-release-${ENSEMBL_VERSION}/modules/Bio ${SITE_PERL}

perl -MBio::EnsEMBL::ApiVersion -e 'print qq{If you see this, means ensembl installation successful.\n}'   #测试ensembl perl模块是否正确安装并成功加载
#-M选项（加载指定的模块）。Bio::EnsEMBL::ApiVersion模块（存储和访问ensembl api的版本信息）。-e选项（直接执行perl代码）。
#qq{...}字符串引号的表示方法（用于创建包含换行符等特殊字符的字符串）。\n是perl中转义字符，换行。（n为newline）

unset ENSEMBL_VERSION     #unset命令（删除/清除shell中变量或函数）
