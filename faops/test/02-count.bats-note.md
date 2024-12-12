# 还原为命令
```
#!/usr/bin/env bats

load test_helper

@test "count: read from file" {
    run bash -c "$BATS_TEST_DIRNAME/../faops count $BATS_TEST_DIRNAME/ufasta.fa | head -n 2"
    assert_equal "#seq${tab}len${tab}A${tab}C${tab}G${tab}T${tab}N" "${lines[0]}"
    assert_equal "read0${tab}359${tab}99${tab}89${tab}92${tab}79${tab}0" "${lines[1]}"
}

@test "count: read from gzipped file" {
    run bash -c "$BATS_TEST_DIRNAME/../faops count $BATS_TEST_DIRNAME/ufasta.fa.gz | head -n 2"
    assert_equal "#seq${tab}len${tab}A${tab}C${tab}G${tab}T${tab}N" "${lines[0]}"
    assert_equal "read0${tab}359${tab}99${tab}89${tab}92${tab}79${tab}0" "${lines[1]}"
}

@test "count: read from stdin" {
    run bash -c "cat $BATS_TEST_DIRNAME/ufasta.fa | $BATS_TEST_DIRNAME/../faops count stdin | head -n 2"
    assert_equal "#seq${tab}len${tab}A${tab}C${tab}G${tab}T${tab}N" "${lines[0]}"
    assert_equal "read0${tab}359${tab}99${tab}89${tab}92${tab}79${tab}0" "${lines[1]}"
}

@test "count: lines of result" {
    run $BATS_TEST_DIRNAME/../faops count $BATS_TEST_DIRNAME/ufasta.fa
    run bash -c "echo \"${output}\" | wc -l | xargs echo "
    assert_equal 52 "${output}"
}

@test "count: mixture of stdin and actual file" {
    run bash -c "cat $BATS_TEST_DIRNAME/ufasta.fa | $BATS_TEST_DIRNAME/../faops count stdin $BATS_TEST_DIRNAME/ufasta.fa"
    run bash -c "echo \"${output}\" | wc -l | xargs echo "
    assert_equal 102 "${output}"
}

@test "count: sum of sizes" {
    run bash -c "
        $BATS_TEST_DIRNAME/../faops count $BATS_TEST_DIRNAME/ufasta.fa \
            | perl -ne '/^total\t(\d+)/ and print \$1'
    "
    assert_equal 9317 "${output}"
}

@test "faCount: without cpg" {
    if ! hash faCount 2>/dev/null ; then
        skip "Can't find faCount"
    fi

    exp=$(faCount $BATS_TEST_DIRNAME/ufasta.fa | perl -anle 'print join qq{\t}, @F[0 .. 6]')
    res=$($BATS_TEST_DIRNAME/../faops count $BATS_TEST_DIRNAME/ufasta.fa)
    assert_equal "$exp" "$res"
}
```
还原后：
```
faops count ufasta.fa | head -n 2

faops count ufasta.fa.gz | head -n 2

cat ufasta.fa | faops count stdin | head -n 2

faops count ufasta.fa | wc -l | xargs echo

cat ufasta.fa | faops count stdin  ufasta.fa | wc -l | xargs echo    
#混合处理stdin和ufasta.fa（重点验证faops逻辑是否正确，能否处理两种输入方式组合起来的情况而不出错，如遗漏或重复计算）

faops count ufasta | perl -ne '/^total\t(\d+)/ and print \$1'

if ! hash faCount 2>/dev/null ; then
    skip "Can't find faCount"
else
    exp=$(faCount ufasta.fa | perl -anle 'print join qq{\t}, @F[0 .. 6]')
    res=$(faops count ufasta.fa)
    if [ "$exp" = "$res" ]; then
        echo "Tset passed."
    else
        echo "Test failed."
    fi
fi

```
`perl -ne '/^total\t(\d+)/ and print \$1'`：从每一行输入中匹配以total开头、后跟制表符和数字的行，提取并打印出该行的数字部分    
`-n`(--loop)为输入的每一行执行一次脚本，但不自动打印每一行。`-e`（--eval）在命令行中直接执行perl代码（简单脚本or正则表达式）    
`/^total\t(\d+)/`正则表达式，`^`匹配行的开始，`total`匹配字符total，`\t`匹配制表符Tab，`(\d+)`匹配一个或多个数字并把它们捕获到一个组里    
`and print \$1`中，`and`perl中逻辑与，`\$1`反义$1按字面意义传给 `print`     

`faCount ufasta.fa | perl -anle 'print join qq{\t}, @F[0 .. 6]'`：    
`faCount`统计fasta中序列信息（序列数量、总长度等）。     
`-a`（--autosplit）自动拆分输出行，将每行按空白字符（空格/制表符）拆分为数组`@F`，每个字段作为一个数组元素。    
`-n`（--loop）。`-l`（--chomp）自动去除每行末尾换行符\n。。`-e`（--eval）    
`print join qq{\t}, @F[0 .. 6]`perl正则脚本，从@F数组提取0到6个元素，并通过制表符\t连成字符串后打印出来     
`@F[0 .. 6]`数组**切片**操作，获取数组中从索引0到索引6的元素。`join`perl函数，用指定的分隔符**连接数组中的元素**   
`qq`perl中特殊操作符，创建双引号字符串，作用跟`".."`一样，但允许使用不同分隔符（不止{}花括号）避免转义字符的混淆。`qq{\t}`等同于`"\t"`