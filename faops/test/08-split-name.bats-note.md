# 还原为命令
```
#!/usr/bin/env bats

load test_helper

@test "split-name: all sequences" {
    mytmpdir=`mktemp -d 2>/dev/null || mktemp -d -t 'mytmpdir'`

    run bash -c "
        $BATS_TEST_DIRNAME/../faops split-name $BATS_TEST_DIRNAME/ufasta.fa $mytmpdir \
        && find $mytmpdir -name '*.fa' | wc -l | xargs echo
    "
    assert_equal 50 "${output}"

    rm -fr ${mytmpdir}
}

@test "split-name: size restrict" {
    mytmpdir=`mktemp -d 2>/dev/null || mktemp -d -t 'mytmpdir'`

    run bash -c "
        $BATS_TEST_DIRNAME/../faops filter -a 10 $BATS_TEST_DIRNAME/ufasta.fa stdout \
        | $BATS_TEST_DIRNAME/../faops split-name stdin $mytmpdir \
        && find $mytmpdir -name '*.fa' | wc -l | xargs echo
    "
    assert_equal 44 "${output}"

    rm -fr ${mytmpdir}
}
```
还原后：
```
#把fasta按序列名字拆分并查询fasta文件有多少条序列
mytmpdir=`mktemp -d 2>/dev/null || mktemp -d -t 'mytmpdir'`
faops split-name ufasta.fa $mytmpdir && find $mytmpdir -name '*.fa' | wc -l | xargs echo
rm -fr ${mytmpdir}

#筛选度长度大于10的序列后按名字拆分fasta并查询序列数量
mytmpdir=`mktemp -d 2>/dev/null || mktemp -d -t 'mytmpdir'`
faop filter -a 10 ufasta.fa stdout | faops split-name stdin $mytmpdir && find $mytmpdir -name '*.fa' | wc- l | xargs echo
rm -fr ${mytmpdir}
```
``mktemp -d 2>/dev/null || mktemp -d -t 'mytmpdir'``中，``...``反引号表示命令替换（执行反引号内的命令并将输出结果赋值给变量mytempdir），类似`$()`  
`mktemp`命令（make temporary）创建临时文件/目录。`-d`选项（directory）创建临时目录。`-t`选项（template）指定临时目录的前缀   
`-name '*.fa'`文件名以.fa结尾的文件    