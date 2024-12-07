#!/bin/bash

BASE_DIR=$( cd "$( dirname "${BASH_SOURCE[0]}")" && pwd )   #获取当前脚本所在目录的绝对路径
#BASH_SOURCE是数组，数组中每个元素表示一个脚本文件的路径，[0]当前脚本。${}是参数扩展的语法，保证变量正确解析。引用和操作变量的值，或对变量执行操作
#dirname命令（提取文件路径中目录部分），cd切换到这个目录，pwd命令（print of working directory输出当前工作目录的绝对路径）

cd "$(BASE_DIR)" || exit

# pip
PYPI_MIRROR=https://mirrors.nju.edu.cn/pypi/web/simple

#pip3 install -i ${PYPI_MIRROR} --upgrade pip setuptools    #-i（index-url指定索引/镜像url）。升级pip和setuptools

pip3 install -i ${PYPI_MIRROR} pysocks cryptography
pip3 install -i ${PYPI_MIRROR} virtualenv
pip3 install -i ${PYPI_MIRROR} more-itertools zipp setuptools-scm
pip3 install -i ${PYPI_MIRROR} numpy matplotlib
pip3 install -i ${PYPI_MIRROR} pandas scipy jupyter sympy
pip3 install -i ${PYPI_MIRROR} lxml statsmodels patsy h5py
pip3 install -i ${PYPI_MIRROR} beautifulsoup4 scikit-learn seaborn

#HDF5_DIR=$(brew --prefix hdf5) pip install h5py tables   #HDF5是h5py和tables的底层依赖库，指定其安装路径，确保pip编译时能找到所需库文件

pip3 install -i ${PYPI_MIRROR} h5py tabulate # TBB

# PPanGGOLiN
pip3 install -i ${PYPI_MIRROR} tqdm tables networkx dataclasses
pip3 install -i ${PYPI_MIRROR} plotly gmpy2 colorlover bokeh

# antismash
pip3 install -i ${PYPI_MIRROR} helperlibs jinja2 joblib jsonschema markupsafe pysvg pyscss
pip3 install -i ${PYPI_MIRROR} biopython bcbio-gff

pip3 install -i ${PYPI_MIRROR} Circle-Map cutadapt importlib-metadata

pip3 install -i ${PYPI_MIRROR} deeptools


# poetry can search packages
# curl -sSL https://install.python-poetry.org | python3 -