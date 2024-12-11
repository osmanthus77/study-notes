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
faops count <in.fa> [more_files.fa]
```

输入：
```
faops count in.1fa
```

