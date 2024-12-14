# 还原为命令
```
#!/usr/bin/env bats

load test_helper

@test "interleave: empty seqs count" {
    exp=$($BATS_TEST_DIRNAME/../faops size $BATS_TEST_DIRNAME/ufasta.fa | grep "\s0" | wc -l | xargs echo)
    res=$($BATS_TEST_DIRNAME/../faops interleave $BATS_TEST_DIRNAME/ufasta.fa $BATS_TEST_DIRNAME/ufasta.fa.gz | grep "^$" | wc -l | xargs echo)
    assert_equal "$(($exp * 2))" "$res"
}

@test "interleave: empty seqs count (single)" {
    exp=$($BATS_TEST_DIRNAME/../faops size $BATS_TEST_DIRNAME/ufasta.fa | grep "\s0" | wc -l | xargs echo)
    res=$($BATS_TEST_DIRNAME/../faops interleave $BATS_TEST_DIRNAME/ufasta.fa | grep "^$" | wc -l | xargs echo)
    assert_equal "$exp" "$res"
}

@test "interleave: fq" {
    run bash -c "
        $BATS_TEST_DIRNAME/../faops interleave -q $BATS_TEST_DIRNAME/R1.fq.gz $BATS_TEST_DIRNAME/R2.fq.gz |
            grep '^!$' |
            wc -l
    "
    assert_equal 0 "${output}"
}

@test "interleave: fq (single)" {
    run bash -c "
        $BATS_TEST_DIRNAME/../faops interleave -q $BATS_TEST_DIRNAME/R1.fq.gz |
            grep '^!$' |
            wc -l
    "
    assert_equal 25 "${output}"
}
```
还原后：
```
#筛取空白序列并计数（双端合并）
faops size ufasta.fa | grep "\s0" | wc -l | xargs echo
faops interleave ufasta.fa ufasta.fa.gz | grep "^$" | wc -l | xargs echo

#筛取空白序列并计数（单端序列）
faops size ufasta.fa | grep "\s0" | wc -l | xargs echo
faops interleave ufasta.fa | grep "^$" | wc -l | xargs echo

#合并fq格式
faops interleave -q R1.fq.gz R2.fq.gz ｜ grep '^!$' | wc -l

#只合并单端测序，结果中包含！感叹号
faops interleave -q R1.fq.gz ｜ grep '^!$' | wc -l

```
`grep "^$"`查找所有空行，`^`行首锚点（匹配行的开头），`$`行尾锚点（匹配行的末尾），两个组合起来表示空行
`grep "^$"`查找只包含一个感叹号！的行