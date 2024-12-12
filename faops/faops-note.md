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
### 4.masked序列掩蔽
`masked`掩蔽掉某些特定的区域或字符，通常在基因组分析中，处理包含低复杂度、重复区域或不需要的区域的序列。包括重复序列、N碱基、下划线等    
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