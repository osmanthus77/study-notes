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
brew install lastz diamond paml fasttree iqtree # raxml     #lastz基因组精确比对 diamond超快速的序列比对 paml最大似然法进行序列进化模型
#fasttree大规模数据初步构树  iqtree高效高精确构树   raxml大规模或精确构树分析
brew install fastani mash   #计算基因组相似性
brew install raxml --without-open-mpi   #安装时不使用Open MPI（开放消息传递接口，MPI可以利用多个节点/CPU核心分布式计算）
brew install --force-bottle newick-utils  #强制使用预编译的二进制文件包bottle而不从源码编译安装  newick-utils（utilities）快速处理newick格式系统发育树
brew install fastqc # kat    #fastqc测序数据质控  kat（kmer analysis toolkit）基于k-mer分析评估可视化测序数据质量特性，特别是基因组装和序列比较
brew install bowtie bowtie2 bwa  #reads比对到参考基因组  
#bowtie（小于50bp全局比对） bowtie2（长达1000bp局部/全局比对） bwa（burrows-wheeler aligner，基因组学）
brew install samtools picard-tools  #samtools处理sam/bam/cram格式文件转换、排序、索引etc  
#picard-tools基于java编写的处理sam/bam文件，功能更高级（标记重复序列、排序、索引、收集比对信息）
brew install stringtie hisat2 # tophat cufflinks  
#stringtie（对比对后reads组装，生成转录本注释并估算表达水平，cufflinks高替）hisat2（RNA-seq比对参考基因组，生成bam，tophat2高替）
brew install seqtk minimap2 minigraph #gfatools    #seqtk对fasta/fastq格式预处理（子采样、质量过滤、转换etc）  minimap2长序列快速比对参考基因组
#minigraph构建基因组图结构（gfa格式，比线性参考基因组更好表示变异）  gfatools处理gfa格式（转换、统计信息、提取路径）
brew install genometools # igvtools   #genometools处理分析基因组数据（尤其gff/gtf格式的基因组注释文件）
#igvtools（intergrative genomics viwer tools）与igv配套用，将基因组数据转为igv能读取的格式。igv可在图形界面查看基因组数据、比对结果、变异信息
brew install --build-from-source snp-sites #macOS bottles broken  
#snp-sites处理分析变异数据，vcf文件提取snp信息并生成适合下游分析的多序列比对（fasta/phylip格式）
#--build-from-source不使用bottles而是从源码编译snp-sites工具，在mac中bottles出问题时
brew install bcftools   #bcftools处理vcf(variant call format)和bcf(binary call format)，常存储变异信息（snp和indels等），分析基因组变异信息

brew install edirect   #ncbi提供的一组工具，从ncbi获取数据本地分析。esearch查询ncbi获取结果，efetch提取数据，elink获取链接，esummary获取简要信息
brew install sratoolkit   
#ncbi提供处理sra（sequence read archive）数据。prefetch下载sra数据，fastq-dump将sra转fastq，vdb-validate验证sra数据库文件完整性。

# less used
brew install augustus prodial prokka   #augustus在不同物种基因组中预测基因的位置和结构（主要真核基因组），prodial针对原核生物进行基因组注释
#prokka快速全面大规模注释原核生物基因组
brew install megahit spades sga   #megahit大规模高复杂（illumina）的短read基因组组装，基于图形的组装算法，宏基因组/单个物种基因组组装。
#spades短read组装，多阶段组装策略，宏基因组/比较基因组。sga基于字符串图的组装，短read，小型基因组。
brew install canu   #长read基因组组装，PacBio或OxfordNanopore平台，高效、提供错误纠正
brew install quast --HEAD   #quast（Quality Assessment Tool for Genome Assemblies）对基因组组装质量评估比较。--HEAD从最新源代码安装
brew install ntcard   #分析基因组数据中snp和基因型数据，计算不同基因型的多样平和频率，评估群体遗传多样性
brew install gatk freebayes   #freebayes从高通量测序数据中分析多样性和变异检测，主要snp/indel/结构变异，利用贝叶斯方法评估，适用单倍型分型/群体基因组
#gatk(Genome Analysis Toolkit)broad institute开发工具组，分析高通量基因组，尤其变异检测、基因组分析、基因型质控、基因组注释

# brew unlink proj
brew install --force-bottle blast

echo "==> Custom taq"
brew tap wang-q/tap
brew install faops multiz sparsemem intspan   #faops专门操作fasta格式（序列提取、转换格式、序列统计etc） multiz多序列比对（搭配blastz）用于进化分析/基因组注释
#sparsemem内存优化的比对工具，比对基因组中长片段找同源区域，大规模数据。 intspan处理区间interval数据，分析染色体上基因组区域、处理大规模变异位点etc
# brew install multiz --cc=gcc   #--cc参数（指定使用c编译器），gcc（GNU Compiler collection），指定使用gcc编译器安装multiz
# brew install jrunlist jrange   #

echo "==> circos"   #circos可视化基因组数据，1⃣️环形布局呈现，展示基因组间相似性、重排、变异和关联
brew install wang-q/tap/circos@0.69.9

echo "==> RepeatMasker"    #识别和屏蔽基因组中重复序列，以来rmblast工具序列搜索
brew install hmmer easel    #hmmer基于隐马尔可夫模型（Hiddern Markov Model）的序列分析，比对、分类和注释。
#easel是hmmer中的工具集，是hammer的底层依赖，序列格式转换、数据清理、简单统计

brew install wang-q/tap/rmblast@2.14.1   #rmblast(RepeatMasker Blast)，搜索重复序列，加速repeatmasker分析
# brew link rmblast@2.14.1 --overwrite   
#link命令（将安装的软件包链接到系统的全局可用路径，如/bin/，使得可从命令行直接访问使用。--overwrite（如目标位置出现重复则覆盖旧版本）

brew install wang-q/tap/trf@4   #trf(Tandem Repeat Finder)检测串联重复序列、报告详细信息
#一个/多个序列在基因组连续重复出现（微卫星Microsatellites、中卫星Minisatellites），影响基因表达等

brew install wang-q/tap/repeatmasker@4.1.1

# Config repeatmasker   #给repeatmasker配置环境、参数等，确保可以正确识别和屏蔽重复序列
pip3 install h5py     #h5py是一个python库（读取和写入HDF5格式文件，Hierarchical Data Format version5储存大规模数据）

cd $(brew --prefix)/Cellar/repeatmasker@4.1.1/4.1.1/libexec
perl configure \      #用perl运行命令。configure是repeatmasker的配置脚本。配置环境并设置外部工具路径
    -hmmer_dir=$(brew --prefix)/bin \    #指定hmmer工具的路径
    -rmblast_dir=$(brew --prefix)/Cellar/rmblast@2.14.1/2.14.1/bin \
    -libdir=$(brew --prefix)/Cellar/repeatmasker@4.1.1/4.1.1/libexec/Libraries \    #指定repeatmasker使用的库文件目录路径
    -trf_prgm=$(brew --prefix)/bin/trf \
    -default_search_engine=rmblast    #指定默认的序列比对引擎为rmblast

cd -    #快捷命令，返回上一个工作目录

#echo "==> Config repeatmasker"
#wget -N -P /tmp https://github.com/egateam/egavm/releases/download/20170907/repeatmaskerlibraries-20140131.tar.gz
# -N（--timestamping时间戳，只下载更新的文件，保持文件的最新版本）  -P（--directory-prefix，指定文件下载的保存路径），保存到/tmp目录下
#
#pushd $(brew --prefix)/opt/repeatmasker/libexec
#rm -fr lib/perl5/x86_64-linux-thread-multi/   #rm(remove)  -r(--recursive)
#rm Libraries/RepeatMasker.lib*    #RepeatMasker.lib*以RepeatMasker.lib开头的所有文件
#rm Libraries/DfamConsensus.embl
#tar zxvf /tmp/repeatmaskerlibraries-20140131.tar.gz  #tar（tape archive） z(--gzip) x(--extract) v(--verbose) f(--file)
#sed -i".bak" 's/\/usr\/bin\/perl/env/' configure.input  #configure.input中/usr/bin/perl替换为env
#./configure < configure.input   #./configure脚本文件，准备和配置repeatmasker的编译环境，源代码提供的。<重定向，从后面文件读取数据
#popd  #pushd反操作