# 还原为命令
```
#!/usr/bin/env bats

load test_helper

@test "dazz: empty seqs count" {
    exp=$($BATS_TEST_DIRNAME/../faops size $BATS_TEST_DIRNAME/ufasta.fa | grep "\s0" | wc -l | xargs echo)
    res=$($BATS_TEST_DIRNAME/../faops dazz -a $BATS_TEST_DIRNAME/ufasta.fa stdout | grep "0_0" | wc -l | xargs echo)
    assert_equal "$exp" "$res"
}

@test "dazz: deduplicate seqs" {
    exp=$($BATS_TEST_DIRNAME/../faops size $BATS_TEST_DIRNAME/ufasta.fa $BATS_TEST_DIRNAME/ufasta.fa.gz | grep "\s0" | wc -l | xargs echo)
    res=$(gzip -d -c -f $BATS_TEST_DIRNAME/ufasta.fa $BATS_TEST_DIRNAME/ufasta.fa.gz | $BATS_TEST_DIRNAME/../faops dazz stdin stdout | grep "0_0" | wc -l | xargs echo)
    assert_equal "$(($exp / 2))" "$res"
}

@test "dazz: duplicated seqs" {
    exp=$($BATS_TEST_DIRNAME/../faops size $BATS_TEST_DIRNAME/ufasta.fa $BATS_TEST_DIRNAME/ufasta.fa.gz | grep "\s0" | wc -l | xargs echo)
    res=$(gzip -d -c -f $BATS_TEST_DIRNAME/ufasta.fa $BATS_TEST_DIRNAME/ufasta.fa.gz | $BATS_TEST_DIRNAME/../faops dazz -a stdin stdout | grep "0_0" | wc -l | xargs echo)
    assert_equal "$exp" "$res"
}
```
还原后：
```
#搜索大小为0（格式为空格后跟数字0）的序列并查询行数（即有多少条大小为0的序列）
faops size ufasta.fa | grep "\s0" | wc -l | xargs echo
faops dazz -a ufasta.fa stdout | grep "0_0" | wc -l | xargs echo

#强制解压作为标准输出后faops dazz把序列信息标准化，再搜索大小为0的序列（dazz格式为0_0）并统计数量
faops size ufasta.fa ufasta.fa.gz | grep "\s0" | wc -l | xargs echo
gzip -d -c -f ufasta.fa ufasta.fa.gz | faops dazz stdin stdout | grep "0_0" | wc -l | xargs echo

faops size ufasta.fa ufasta.fa.gz | grep "\s0" | wc -l | xargs echo
gzip -d -c -f ufasta.fa ufasta.fa.gz | faops dazz -a stdin stdout | grep "0_0" | wc -l | xargs echo
```
`\s0`表示空白字符后跟数字0，`0_0`是faops dazz命令输出结果中标题行中内容（>read/10/0_0和>read/11/0_141），`0_0`是位置标识   
`-a`faops dazz选项（duplicated sequence不删除重复序列）   
`gzip -d -c -f`中，`-d`（decompress）解压缩，`-c`（stdout）将解压后内容输出到标准输出，`-f`（force）强制操作