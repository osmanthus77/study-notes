faops operating sequences in fasta format处理fasta格式序列，参考了seqtk、ufasta
seqtk高效处理大规模fasta数据（转格式、提取子序列、统计信息等）。ufasta简化优化fasta文件

使用说明
用法：faops <command> [option] <arguments></arguments>
option可选的参数，必须跟在command后，命令不同option不同
arguments必要参数，输入的数据/参数，eg文件路径/数据等

commands：
    help           print this message
    count          count base statistics in FA file(s)  #统计碱基数据，每个碱基数量、分布etc
    size           count total bases in FA file(s)     #统计每个序列对应长度
    masked         masked (or gaps) regions in FA file(s)    #提取fasta中被掩码masked或存在缺失gaps区域的部分，masked测序过程中不确定部分
    frag           extract sub-sequences from a FA file    #从fasta提取子序列
    rc             reverse complement a FA file    #对fasta序列进行反向互补操作，3'逆转到5'
    one            extract one fa record     #提取记录，多序列中提取特定一个
    some           extract some fa records    
    order          extract some fa records by the given order   #根据给定顺序提取记录
    replace        replace headers from a FA file     #替换头部信息（标题行描述信息）
    filter         filter fa records    #过滤/修改fasta中数据
    split-name     splitting by sequence names    #根据序列名称拆分文件
    split-about    splitting to chunks about specified size     #根据指定文件大小拆分文件
    n50            compute N50 and other statistics     #N50基因组组装质量评估的统计量，累计的contig长度达到总长度的50%时的那一个contig长度
    dazz           rename records for dazz_db     #给dazz_db重命名记录/转格fasta为dazz_db，dazzler基于dag（有向无环图）格式的基因组数据存储工具
    interleave     interleave two PE files    #交织两个pe文件，两个配对末端序列文件（正向+反向）交织合并为一个文件
    region         extract regions from a FA file    #提取特定区域

Examples：
#rc反向互补
faops rc test/ufasta.fa out.fa    #rc命令，对test/ufasta.fa进行反向互补，输出文件out.fa。输出序列名称前会有RC_，区分原始和反向互补序列
faops rc -n test/ufasta.fa out.fa   #-n（--no-name-change保留原始的序列名称，不添加RC_前缀）

#提取序列one/some/order
faops some test/ufasta.fa list.file out.fa    
#根据提供的list.file（给出需要的序列名称，一行一个名称）提取对应序列。order再把list中按字母/数字自动排序后输出
cat test/ufasta.fa | faops some stdin list.file stdout    #跟上一行效果一样，不过提起出的序列是标准输出（终端显示），不是out.fa文件里

#按头部信息（标题行名称）排序
faops order test/ufasta.fa \    #按给定顺序排序，给定顺序为下一行
    <(cat test/ufasta.fa | grep '>' | sed 's/>//' | sort) \   #按序列名称的字母顺序排序
    out.fa
#<(...)命令替换，命令的输出作为输入传递给faops order。sort按字母顺序排序
#单引号'..'用于保护字符不被shell解析/扩展，将引号里所有字符视为字面意思。
#双引号"..."允许某些特殊字符（如变量/命令替换）在字符串中进行解析，扩展变量/执行命令替换可用

#按长度排序
faops order test/ufasta.fa \
    <(faops size test/ufasta.fa | sort -n -r -k2,2 | cut -f 1) \
    out2.fa
#sort的-n（-numeric-sort数字排序）-r（--reverse逆序，从大到小）-k2,2（--key=2,2  指定排序的列，第二列到第二列）
#cut命令（从每行中提取指定的字段），-f（--fields=1，提取每行第一个字段，分隔符为字段的界定），在这指序列名称

#序列整理为每行最多80字符
faops filter -l 80 test/ufasta.fa out.fa     #-l 80（--line-length=80）

#所有字符写在一行
faops filter -l 0 test/ufasta.fa out.fa    #-l 0每行长度设置为0，即每个序列所有碱基写在一行，不换行

#fatsq转fasta
faops filter -l 0 in.fq out.fa   #fastq转为fasta且不换行

#计算N50并清理输出结果
faops n50 -H test/ufasta.fa    #-H（--clean）

#N75
faops n50 -N 75 test/ufasta.fa    #-N 75（--n-percent=75）

#N90 with基因组大小
faops n50 -N 90 -S -A -g 10000 test/ufasta.fa      #-S（--sum输出所有contig长度的累计和） - A（---average平均contig长度）
#-g 10000（--genome-size=10000指定估计的基因组大小为10000）
#没有-g时，N90只反映组装的contig长度和组装质量。有-g时，计算N90时考虑基因组预期大小，并允许根据预期大小进行对结果归一化
#归一后可判断组装是否足够完整，是否覆盖了整个基因组，如果归一后N90很小，说明组装结果在基因组中覆盖度不够。

#下载安装
git clone https://github.com/wang-q/faops
cd faops
make     #make自动化构建工具，根据根目录中makefiles文件中定义的规则自动编译源代码
brew install wang-q/tap/faops

#Tests
#brew install bats-core
make test     #make test命令测试套件，测试代码/脚本的正确性和功能是否符合预期