# faops使用说明
faops operating sequences in fasta format处理fasta格式序列，参考了seqtk、ufasta  

seqtk高效处理大规模fasta数据（转格式、提取子序列、统计信息等）。ufasta简化优化fasta文件  

## 安装与编译
### homebrew安装
```
brew install wang-q/tap/faops
```

### 编译
```
git clone https://github.com/wang-q/faops
cd faops
make 
```

## 使用说明
用法：faops \<command\> [option] \<arguments\>     
option可选的参数，必须跟在command后，命令不同option不同    
arguments必要参数，输入的数据/参数，eg文件路径/数据等    

### 1.获取帮助faops help
终端输入faops help，输出各种帮助说明    
输入：
```
faops help
```

输出：
```
Usage:     faops <command> [options] <arguments>
Version:   0.8.21

Commands:
    help           print this message
    count          count base statistics in FA file(s)
    size           count total bases in FA file(s)
    masked         masked (or gaps) regions in FA file(s)
    frag           extract sub-sequences from a FA file
    rc             reverse complement a FA file
    one            extract one fa record
    some           extract some fa records
    order          extract some fa records by the given order
    replace        replace headers from a FA file
    filter         filter fa records
    split-name     splitting by sequence names
    split-about    splitting to chunks about specified size
    n50            compute N50 and other statistics
    dazz           rename records for dazz_db
    interleave     interleave two PE files
    region         extract regions from a FA file

Options:
    There're no global options.
    Type "faops command-name" for detailed options of each command.
    Options *MUST* be placed just after command.
```

### 2.count统计碱基组成
faops count统计fatsa中各序列总长和每种碱基的数量     
用法：
```
faops count <input.fa> [more_files.fa]
```

输入：
```
faops count test.fa
```
其中，test.fa内容为：
```
>read0
tCGTTTAACCCAAatcAAGGCaatACAggtGggCCGccCatgTcAcAAActcgatGAGtgGgaAaTGgAgTgaAGcaGCAtCtGctgaGCCCCATTctc
tAgCggaaaATGgtatCGaACcGagataAGtTA
AacCgcaaCgGAtaagGgGcgGGctTCAaGtGAaGGaAGaGgGgTTcAaaAgGccCgtcGt
CaaTcAaCtAAggcGgaTGtGACactCCCCtAtTtcaaGTCTTctaCccTtGaTaCGaTtcgCGTtcGaGGaGcT
ACaTTAaccaaGtTaatgCGAGCGcCtgCGaAcTTGccAAgTCaGCtgctCTgttCtcAggTaCAcAaGTcagccAtTGTGTCGaCGCTCT
>read1
taGGCGcGGgCggtgTgGATTAaggCAGaggtTgCGCGCtTgaTAaAACTacgtaACatcggGAAcTtcgaccGgtCTCgGccCtatAtgaTtCcGatc
GCaTaTC
>read2
AtagcAagCtcAgttcaACttCAcCGGT
A
AaTtcttgTAGtgTcTCgacCgcCcCctTGTACtgtaGG
caAtaGTaaTgAcTagGaCGTaagagAcCaccaCagGAAgAGAATccgCGaAtcCcTcacCCttGGTC
ctGgAttttgcgcgTggtatgagGgAGtctcaATTGTCaccTaC
GTatcccAGCgCtAcAcaAGAcTaCAtCTggCatTAG
>read3
gTTTTcttaGgCgtccCGAAgcAtCtCTagCCgggGgTAatctccAgg
tgTgTttGTTaCCtcCTCGtgACCC
>read4
GcgATCgCtcACTtGcGCTtcCCtggCtGctcTGgtctgtGca
gctAGtAAccAgtaTCtaGgtGAGACCaTGgcg
>read5
AatcccAgAttcttCcTaTAGgGTagTaAcgcggTgGAgCTGCagAGGTaAgccGtcg
GaGGGgagGcAagtGCCggtTGcGAGtcCaTgCcTtCAGgccCtcG
CgCTgAcCCtaCgtTtAAa
TacAggGttggTccTcaAgcGtcTTCGAtGcTcTaggAGGgaGcCTGgcTAaCTGttCTtGatTGtCgATTtCgAaggAGattagcTTgccg
```
输出：
```
#seq	len	A	C	G	T	N
read0	359	99	89	92	79	0
read1	106	24	25	32	25	0
read2	217	57	58	49	53	0
read3	73	10	21	19	23	0
read4	76	12	22	22	20	0
read5	215	43	51	67	54	0
total	1046	245	266	281	254	0
```
### 3.size统计序列长度
`size`可统计fasta文件中序列总长度    
size统计长度包括序列中空格数，count只统计碱基数量不包括空格    
用法：
```
faops size <input.fa> [more_files.fa]
```
输入：
```
faops size test.fa
```
输出：
```
read0	359
read1	106
read2	217
read3	73
read4	76
read5	215
```
### 4.masked定位冗余碱基
`masked`快速找到序列文件中冗余碱基的位置，包括重复序列、N碱基、下划线等    
ps：重复碱基在fasta中为小写字母    
用法：
```
faops masked [options] <input.fa> [more_files.fa] 
```
选项：
```
-g     仅定位N碱基和下划线位置
```
输入：
```
faops masked test2.fa
```
test2.fa内容为：
```
>read3
gTTTTcttaGgCgtccCGAAgcAtCtCTagCCgggGgTAatctccAgg
tgTgTttGTTaCCtcCTCGtgACCC
```
输出：
```
read3:1
read3:6-9
read3:11
read3:13-16
read3:21-22
read3:24
read3:26
read3:29-30
read3:33-35
read3:37
read3:40-45
read3:47-50
read3:52
read3:54-55
read3:59
read3:62-63
read3:68-69
```


### 5.frag提取指定序列片段
从序列文件提取指定区域，默认对第一个序列进行    
用法：
```
faops frag [options] <input.fa> start end <output.fa>
```
选项：
```
-l       length指定提取片段的分行宽度，默认80
```
输入：
```
faops frag -l 50 test.fa 10 70 fragout.fa
```

输出：fragout.fa文件内容    
```
>read0:10-70
CCAAatcAAGGCaatACAggtGggCCGccCatgTcAcAAActcgatGAGt
gGgaAaTGgAg
```

### 6.rc反向/互补序列
rc（reverse complement）获取反向或互补序列，默认头部信息前添加`RC_`    
用法：
```
faops rc [options] <input.fa> <output.fa> 
```

选项：
```
-n      保持序列名称相同,不添加RC_
-r      仅反向，添加R_
-c      仅互补，添加C_
-f      仅对给定列表中序列进行反向/互补，-f后接文件
-l      输出时序列分行宽度，默认80
```

输入：
```
faops rc -n -l 80 -f list.file test.fa rcout.fa
```

list.file内容：
```
read4
read0
```

输出：rcout.fa文件内容
```
>read0
AGAGCGtCGACACAaTggctgACtTgTGtAccTgaGaacAGagcaGCtGAcTTggCAAgTtCGcaGgCGCTCGcattAaC
ttggtTAAtGTAgCtCCtCgaACGcgaAtCGtAtCaAggGtagAAGACttgaAaTaGGGGagtGTCaCAtcCgccTTaGt
TgAttGaCgacGggCcTttTgAAcCcCtCTtCCtTCaCtTGAagCCcgCcCcttaTCcGttgcGgtTTAaCTtatctCgG
TtCGatacCATtttccGcTagagAATGGGGCtcagCaGaTGCtgCTtcAcTcCAtTtcCcaCTCatcgagTTTgTgAcat
GggCGGccCaccTGTattGCCTTgatTTGGGTTAAACGa
>read1
taGGCGcGGgCggtgTgGATTAaggCAGaggtTgCGCGCtTgaTAaAACTacgtaACatcggGAAcTtcgaccGgtCTCg
GccCtatAtgaTtCcGatcGCaTaTC
>read2
AtagcAagCtcAgttcaACttCAcCGGTAAaTtcttgTAGtgTcTCgacCgcCcCctTGTACtgtaGGcaAtaGTaaTgA
cTagGaCGTaagagAcCaccaCagGAAgAGAATccgCGaAtcCcTcacCCttGGTCctGgAttttgcgcgTggtatgagG
gAGtctcaATTGTCaccTaCGTatcccAGCgCtAcAcaAGAcTaCAtCTggCatTAG
>read3
gTTTTcttaGgCgtccCGAAgcAtCtCTagCCgggGgTAatctccAggtgTgTttGTTaCCtcCTCGtgACCC
>read4
cgcCAtGGTCTCacCtaGAtacTggTTaCTagctgCacagacCAgagCaGccaGGgaAGCgCaAGTgaGcGATcgC
>read5
AatcccAgAttcttCcTaTAGgGTagTaAcgcggTgGAgCTGCagAGGTaAgccGtcgGaGGGgagGcAagtGCCggtTG
cGAGtcCaTgCcTtCAGgccCtcGCgCTgAcCCtaCgtTtAAaTacAggGttggTccTcaAgcGtcTTCGAtGcTcTagg
AGGgaGcCTGgcTAaCTGttCTtGatTGtCgATTtCgAaggAGattagcTTgccg
```

### 7.one/some提取特定序列
根据给定列表中序列名称提取特定序列。列表中包含序列名称，以回车为分隔    
用法：
```
faops some [options] <input.fa> <list.file> <output.fa>
```

选项：
```
-i     反向提取名称不在列表中的序列
-l     输出时序列分行宽度
```
输入：
```
faops some -l 80 test.fa list.file someout.fa
```

输出：someout.fa文件内容    
```
>read0
tCGTTTAACCCAAatcAAGGCaatACAggtGggCCGccCatgTcAcAAActcgatGAGtgGgaAaTGgAgTgaAGcaGCA
tCtGctgaGCCCCATTctctAgCggaaaATGgtatCGaACcGagataAGtTAAacCgcaaCgGAtaagGgGcgGGctTCA
aGtGAaGGaAGaGgGgTTcAaaAgGccCgtcGtCaaTcAaCtAAggcGgaTGtGACactCCCCtAtTtcaaGTCTTctaC
ccTtGaTaCGaTtcgCGTtcGaGGaGcTACaTTAaccaaGtTaatgCGAGCGcCtgCGaAcTTGccAAgTCaGCtgctCT
gttCtcAggTaCAcAaGTcagccAtTGTGTCGaCGCTCT
>read4
GcgATCgCtcACTtGcGCTtcCCtggCtGctcTGgtctgtGcagctAGtAAccAgtaTCtaGgtGAGACCaTGgcg
```

### 8.order给定顺序重新排序
faops order将序列文件数据全部读入内存，按照提供顺序输出到新文件    
用法：
```
faops order [options] <input.fa> <list.file> <output.fa> 
```

选项：
```
-l     序列分行长度，默认80
```

输入：按序列长度排序
```
faops order test.fa \
    <(faops size test.fa | sort -n -r -k2,2 | cut -f 1) \
    orderout.fa
```
`<(...)`进程替换，(...)的内部命令输出被当作临时文件路径传递给前面命令（faops order）   
`sort -n -r -k2,2`对结果排序，`-n`（numeric）按数值排序，`-r`（reverse）倒序从大到小，`-k2,2`（key）指定排序的列，第二列到第二列     
`cut -f 1`从文本提取指定列的数据，`-f`（field）提取的字段，`=1`这里提取每行第一字段    
输出：orderout.fa文件内容
```
>read0
tCGTTTAACCCAAatcAAGGCaatACAggtGggCCGccCatgTcAcAAActcgatGAGtgGgaAaTGgAgTgaAGcaGCA
tCtGctgaGCCCCATTctctAgCggaaaATGgtatCGaACcGagataAGtTAAacCgcaaCgGAtaagGgGcgGGctTCA
aGtGAaGGaAGaGgGgTTcAaaAgGccCgtcGtCaaTcAaCtAAggcGgaTGtGACactCCCCtAtTtcaaGTCTTctaC
ccTtGaTaCGaTtcgCGTtcGaGGaGcTACaTTAaccaaGtTaatgCGAGCGcCtgCGaAcTTGccAAgTCaGCtgctCT
gttCtcAggTaCAcAaGTcagccAtTGTGTCGaCGCTCT
>read2
AtagcAagCtcAgttcaACttCAcCGGTAAaTtcttgTAGtgTcTCgacCgcCcCctTGTACtgtaGGcaAtaGTaaTgA
cTagGaCGTaagagAcCaccaCagGAAgAGAATccgCGaAtcCcTcacCCttGGTCctGgAttttgcgcgTggtatgagG
gAGtctcaATTGTCaccTaCGTatcccAGCgCtAcAcaAGAcTaCAtCTggCatTAG
>read5
AatcccAgAttcttCcTaTAGgGTagTaAcgcggTgGAgCTGCagAGGTaAgccGtcgGaGGGgagGcAagtGCCggtTG
cGAGtcCaTgCcTtCAGgccCtcGCgCTgAcCCtaCgtTtAAaTacAggGttggTccTcaAgcGtcTTCGAtGcTcTagg
AGGgaGcCTGgcTAaCTGttCTtGatTGtCgATTtCgAaggAGattagcTTgccg
>read1
taGGCGcGGgCggtgTgGATTAaggCAGaggtTgCGCGCtTgaTAaAACTacgtaACatcggGAAcTtcgaccGgtCTCg
GccCtatAtgaTtCcGatcGCaTaTC
>read4
GcgATCgCtcACTtGcGCTtcCCtggCtGctcTGgtctgtGcagctAGtAAccAgtaTCtaGgtGAGACCaTGgcg
>read3
gTTTTcttaGgCgtccCGAAgcAtCtCTagCCgggGgTAatctccAggtgTgTttGTTaCCtcCTCGtgACCC
```

### 9.replace替换序列名称/内容
`replace`可替换fasta中**序列名称**或对序列内容进行简单替换    
用法：
```
faops replace [options] <input.fa> <mapping_file> <output.fa>
```
ps：mapping_file内容两列数据，用制表符分隔    
选项：
```
-s     仅将列表文件中的序列改名输出
-l     序列分行宽度，默认80
```
输入：序列改名
```
faops replace -s test.fa mapping.file replaceout.fa
```
mapping.file文件内容
```
read1	read-1
read4	read+4
```
输出：replaceout.fa
```
>read-1
taGGCGcGGgCggtgTgGATTAaggCAGaggtTgCGCGCtTgaTAaAACTacgtaACatcggGAAcTtcgaccGgtCTCg
GccCtatAtgaTtCcGatcGCaTaTC
>read+4
GcgATCgCtcACTtGcGCTtcCCtggCtGctcTGgtctgtGcagctAGtAAccAgtaTCtaGgtGAGACCaTGgcg
```

### 10.fileter过滤特定长度序列
`filter`可进行长度筛选，配合其他参数还可实现（去除序列名称重复的序列、简化序列名称、过滤N碱基含量高的序列、同一序列写为一行、转换格式等）    
用法：
```
faops filter [options] <input.fa> <output.fa>
```
选项：
```
-a     设置序列大小的下限
-z     设置序列大小的上限
-n     设置N出现次数的上限
-u     unique去除名称重复的序列，保留第一个
-U     序列统一转为大写
-b     把同一序列取消换行并合并为一行
-N     将IUPAC模糊碱基用N替换，IUPAC模糊碱基：DNA序列中出现的不确定性或多样性，ACTG之外的碱基。
-s     将序列名称简化simplify
-l     序列分行长度，默认80
-d     删除序列中非标准的碱基（ACGT外的）
```
输入：
```
faops filter -a 10 -z 150 test.fa filterout.fa
```
输出：filterous.fa文件内容
```
>read1
taGGCGcGGgCggtgTgGATTAaggCAGaggtTgCGCGCtTgaTAaAACTacgtaACatcggGAAcTtcgaccGgtCTCg
GccCtatAtgaTtCcGatcGCaTaTC
>read3
gTTTTcttaGgCgtccCGAAgcAtCtCTagCCgggGgTAatctccAggtgTgTttGTTaCCtcCTCGtgACCC
>read4
GcgATCgCtcACTtGcGCTtcCCtggCtGctcTGgtctgtGcagctAGtAAccAgtaTCtaGgtGAGACCaTGgcg
```

### 11.split-name按照序列名称拆分文件
用法：
```
faops split-name [options] <input.fa> <output.dir>
```
`out.dir`输出文件夹    
选项：
```
-l     序列分行宽度，默认80
```
输入：
```
faops split-name test.fa split-name-out.dir
```
输出：split-name-out.dir中6个fasta文件
```
>read0
tCGTTTAACCCAAatcAAGGCaatACAggtGggCCGccCatgTcAcAAActcgatGAGtgGgaAaTGgAgTgaAGcaGCA
tCtGctgaGCCCCATTctctAgCggaaaATGgtatCGaACcGagataAGtTAAacCgcaaCgGAtaagGgGcgGGctTCA
aGtGAaGGaAGaGgGgTTcAaaAgGccCgtcGtCaaTcAaCtAAggcGgaTGtGACactCCCCtAtTtcaaGTCTTctaC
ccTtGaTaCGaTtcgCGTtcGaGGaGcTACaTTAaccaaGtTaatgCGAGCGcCtgCGaAcTTGccAAgTCaGCtgctCT
gttCtcAggTaCAcAaGTcagccAtTGTGTCGaCGCTCT
```
```
>read1
taGGCGcGGgCggtgTgGATTAaggCAGaggtTgCGCGCtTgaTAaAACTacgtaACatcggGAAcTtcgaccGgtCTCg
GccCtatAtgaTtCcGatcGCaTaTC
```
后四个文件类似    

### 12.split-about根据指定大小拆分文件
用法：
```
faops split-about [options] <input.fa> <approx_size> <output.dir>
```
`approx_size`指拆分后文件的预期碱基片段的长度，以字节bytes为单位，每个碱基为1字节    
选项：
```
-e     even拆分出来的文件分到的序列数量平均
-m     max parts最多拆分的数量
-l     序列分行宽度，默认80
```
输入：
```
faops split-about -e -m 3 test.fa 70 split-about-out.dir
```
输出：split-about-out.dir中三个fasta文件，分别为000.fa  001.fa   002.fa
000.fa:
```
>read0
tCGTTTAACCCAAatcAAGGCaatACAggtGggCCGccCatgTcAcAAActcgatGAGtgGgaAaTGgAgTgaAGcaGCA
tCtGctgaGCCCCATTctctAgCggaaaATGgtatCGaACcGagataAGtTAAacCgcaaCgGAtaagGgGcgGGctTCA
aGtGAaGGaAGaGgGgTTcAaaAgGccCgtcGtCaaTcAaCtAAggcGgaTGtGACactCCCCtAtTtcaaGTCTTctaC
ccTtGaTaCGaTtcgCGTtcGaGGaGcTACaTTAaccaaGtTaatgCGAGCGcCtgCGaAcTTGccAAgTCaGCtgctCT
gttCtcAggTaCAcAaGTcagccAtTGTGTCGaCGCTCT
>read1
taGGCGcGGgCggtgTgGATTAaggCAGaggtTgCGCGCtTgaTAaAACTacgtaACatcggGAAcTtcgaccGgtCTCg
GccCtatAtgaTtCcGatcGCaTaTC
```
001.fa:
```
>read2
AtagcAagCtcAgttcaACttCAcCGGTAAaTtcttgTAGtgTcTCgacCgcCcCctTGTACtgtaGGcaAtaGTaaTgA
cTagGaCGTaagagAcCaccaCagGAAgAGAATccgCGaAtcCcTcacCCttGGTCctGgAttttgcgcgTggtatgagG
gAGtctcaATTGTCaccTaCGTatcccAGCgCtAcAcaAGAcTaCAtCTggCatTAG
>read3
gTTTTcttaGgCgtccCGAAgcAtCtCTagCCgggGgTAatctccAggtgTgTttGTTaCCtcCTCGtgACCC
```
002.fa:
```
>read4
GcgATCgCtcACTtGcGCTtcCCtggCtGctcTGgtctgtGcagctAGtAAccAgtaTCtaGgtGAGACCaTGgcg
>read5
AatcccAgAttcttCcTaTAGgGTagTaAcgcggTgGAgCTGCagAGGTaAgccGtcgGaGGGgagGcAagtGCCggtTG
cGAGtcCaTgCcTtCAGgccCtcGCgCTgAcCCtaCgtTtAAaTacAggGttggTccTcaAgcGtcTTCGAtGcTcTagg
AGGgaGcCTGgcTAaCTGttCTtGatTGtCgATTtCgAaggAGattagcTTgccg
```

### 13.n50计算N50即一系列值
`n50`计算N50、N75、N90这类值，评估基因组组装质量。含义：按大小排序后，累计的contig长度之和达到全部的总长度的50%时的那一个contig的长度    
用法：
```
faops n50 [options] <input.fa> [more_files.fa]
```
选项：
```
-H     只返回统计值，没有统计值的名称
-N     计算n值，默认50
-S     输出所有contig长度的累计和sum
—A     输出平均contig长度average
-E     计算E值(contig长度加权平均)
-C     输出总的序列数量
-g     指定基因组大小，有-g时计算N值考虑基因组预期大小，并允许根据预期大小对结果归一化，可判断组装是否完整、是否覆盖整个基因组
```
输入：
```
faops n50 -S -A -E -C test.fa
```
输出：
```
N50	217
S	1046
A	174.33
E	233.78
C	6
```

### 14.dazz序列信息标准化
`dazz`将fasta中序列重命名为适配DAZZ_DB的格式，dazz_db是一种高效存储和处理DNA序列数据库的格式。    
具体讲，`dazz`重新命名序列的ID，生成一个数字化的连续编号，不使用原始序列名称。原始的保存在.dazz.fasta.map文件    
用法：
```
faops dazz [option] <input.fa> <output.fa>
```
选项：
```
-p     设置新的序列名称前缀，默认read
-s     起始序号，默认1
-a     不删除重复序列
-l     序列分行宽度，默认80
```
输入：
```
faops dazz -p dazz test.fa dazzout.fa
```
输出：
```
>dazz/1/0_359
tCGTTTAACCCAAatcAAGGCaatACAggtGggCCGccCatgTcAcAAActcgatGAGtgGgaAaTGgAgTgaAGcaGCA
tCtGctgaGCCCCATTctctAgCggaaaATGgtatCGaACcGagataAGtTAAacCgcaaCgGAtaagGgGcgGGctTCA
aGtGAaGGaAGaGgGgTTcAaaAgGccCgtcGtCaaTcAaCtAAggcGgaTGtGACactCCCCtAtTtcaaGTCTTctaC
ccTtGaTaCGaTtcgCGTtcGaGGaGcTACaTTAaccaaGtTaatgCGAGCGcCtgCGaAcTTGccAAgTCaGCtgctCT
gttCtcAggTaCAcAaGTcagccAtTGTGTCGaCGCTCT
>dazz/2/0_106
taGGCGcGGgCggtgTgGATTAaggCAGaggtTgCGCGCtTgaTAaAACTacgtaACatcggGAAcTtcgaccGgtCTCg
GccCtatAtgaTtCcGatcGCaTaTC
>dazz/3/0_217
AtagcAagCtcAgttcaACttCAcCGGTAAaTtcttgTAGtgTcTCgacCgcCcCctTGTACtgtaGGcaAtaGTaaTgA
cTagGaCGTaagagAcCaccaCagGAAgAGAATccgCGaAtcCcTcacCCttGGTCctGgAttttgcgcgTggtatgagG
gAGtctcaATTGTCaccTaCGTatcccAGCgCtAcAcaAGAcTaCAtCTggCatTAG
>dazz/4/0_73
gTTTTcttaGgCgtccCGAAgcAtCtCTagCCgggGgTAatctccAggtgTgTttGTTaCCtcCTCGtgACCC
>dazz/5/0_76
GcgATCgCtcACTtGcGCTtcCCtggCtGctcTGgtctgtGcagctAGtAAccAgtaTCtaGgtGAGACCaTGgcg
>dazz/6/0_215
AatcccAgAttcttCcTaTAGgGTagTaAcgcggTgGAgCTGCagAGGTaAgccGtcgGaGGGgagGcAagtGCCggtTG
cGAGtcCaTgCcTtCAGgccCtcGCgCTgAcCCtaCgtTtAAaTacAggGttggTccTcaAgcGtcTTCGAtGcTcTagg
AGGgaGcCTGgcTAaCTGttCTtGatTGtCgATTtCgAaggAGattagcTTgccg
```
`>dazz/1/0_359`中，/1是读取编号（通常测序中第几条读取序列），/0_359是位置标识（原是读取序列的0到359位提取的子序列）    
### 15.interleave合并双端测序
`interleave`把双端测序的两个文件交错合并    
用法：
```
faops interleave [options] <R1.fa> <R2.fa>
```
选项：
```
-p     设置新的序列名称前缀，默认read
-s     起始序号，默认0
-l     序列分行宽度，默认80
```
输入：
```
faops interleave testR1.fa testR2.fa
```
其中testR1.fa和testR2.fa分别为：
```
>SRR5042715.1625985
AGAGATTCTTGGCGGAGAAACCATAATTGCATCTACTCGTCGCGAACCGCTTTCATCCGGCACAGTATCAAGGTATTTTA
TGCGCGCACGAAAAGCATCAGGTCTTTC
>SRR5042715.5520493
ACTGCTGCGCAGAACTGATGAGCGATCCGAATAGCTCGATGCACGAGGAAGAAGATGATGGCTAAACCAGCGCGAAGACG
ATGTAAAAACGATGAATGCCGGGAATGG
>SRR5042715.4360734
GCCGGATTCGGTATGGCTGCATTCTGCGGTAAGCACGAACTCAGCCAGAACGACAAACAAAAGGCTATCAACTATCTGAT
GCAATTTGCACACAAGGTATCGGGGAAA
```
```
>SRR5042715.1625985
ATTTATATCATTTTACGTTTCTCGTTCAGCTTTTTTATACTAAGTTGGCATTATAAAAAAGCATTGCTTATCAATTTGTT
GCAACGAACAGGTCACTATCAGTCAAAA
>SRR5042715.5520493
TTGCGTTCATTAAATCGGAGTTGAGGTGCCGCAGCAGTTGTCCGGTAATGTCCGGCATCCCACTGAGCAGACGTGAGCGT
TCCGCACGAGATACATGGTAAGTCGCGG
>SRR5042715.4360734
CTGTACCATGGCAATCTCTGCATCTTGCCCCCGGCGTCGCGGCACTACGGCAATAATCCGCATAAGCGAATGTTGCGAGC
ACTTGCAGTACCTTTGCCTTAGTATTTC
```
输出：
```
>read0/1
AGAGATTCTTGGCGGAGAAACCATAATTGCATCTACTCGTCGCGAACCGCTTTCATCCGGCACAGTATCAAGGTATTTTATGCGCGCACGAAAAGCATCAGGTCTTTC
>read0/2
ATTTATATCATTTTACGTTTCTCGTTCAGCTTTTTTATACTAAGTTGGCATTATAAAAAAGCATTGCTTATCAATTTGTTGCAACGAACAGGTCACTATCAGTCAAAA
>read1/1
ACTGCTGCGCAGAACTGATGAGCGATCCGAATAGCTCGATGCACGAGGAAGAAGATGATGGCTAAACCAGCGCGAAGACGATGTAAAAACGATGAATGCCGGGAATGG
>read1/2
TTGCGTTCATTAAATCGGAGTTGAGGTGCCGCAGCAGTTGTCCGGTAATGTCCGGCATCCCACTGAGCAGACGTGAGCGTTCCGCACGAGATACATGGTAAGTCGCGG
>read2/1
GCCGGATTCGGTATGGCTGCATTCTGCGGTAAGCACGAACTCAGCCAGAACGACAAACAAAAGGCTATCAACTATCTGATGCAATTTGCACACAAGGTATCGGGGAAA
>read2/2
CTGTACCATGGCAATCTCTGCATCTTGCCCCCGGCGTCGCGGCACTACGGCAATAATCCGCATAAGCGAATGTTGCGAGCACTTGCAGTACCTTTGCCTTAGTATTTC
```

### 16.region提取序列中指定位置的片段
可使用参数标注提取的正反方向    
用法：
```
faops region [options] <input.fa> <region.txt> <output.fa>
```
选项：
```
-s     在序列名称中标注提取的方向
-l     序列分行宽度，默认80
```
输入：
```
faops region -s test.fa region.file regionout.fa
```
region.file文件内容：
```
read0:10-30,60-80
read3:20-40
read5:10-20
```
输出：regionout.fa文件内容
```
>read0(+):10-30
CCAAatcAAGGCaatACAggt
>read0(+):60-80
gGgaAaTGgAgTgaAGcaGCA
>read3(+):20-40
AgcAtCtCTagCCgggGgTAa
>read5(+):10-20
ttcttCcTaTA
```
