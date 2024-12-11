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

