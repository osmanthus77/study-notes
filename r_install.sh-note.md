#!/bin/bash

BASE_DIR=$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )

cd "${BASE_DIR}" || exit

# font_install() doesn't provide the repo argument
cat <<EOF > .Rprofile      #<<允许多行文本作为输入，碰到EOF标识（单独一行前后不能有空格）结束
# set R mirror
# Bioconductor mirror
options(BioC_mirror="https://mirrors.ustc.edu.cn/bioc")     
# CRAN mirror
options("repos" = c(CRAN="https://mirrors.ustc.edu.cn/CRAN/"))   
EOF

Rscript packages.R

rm .Rprofile

#options（R的函数，设置全局选项/参数），BioC_mirror（Bioconductor包的镜像源选项）
#options的参数可以是命名参数和键值对列表
#"repos"是R全局选项（软件包的下载源），repos是键值对的键，以字符串形式，加上".."。c()创建向量，CRAN=设置CRAN（R包下载源）的镜像站