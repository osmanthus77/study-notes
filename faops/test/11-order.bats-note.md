# 还原为命令
```
#!/usr/bin/env bats

load test_helper

@test "order: inline names" {
    exp=$($BATS_TEST_DIRNAME/../faops filter -l 0 $BATS_TEST_DIRNAME/ufasta.fa stdout | grep -A 1 '^>read12')
    res=$($BATS_TEST_DIRNAME/../faops order -l 0 $BATS_TEST_DIRNAME/ufasta.fa <(echo read12) stdout)
    assert_equal "$exp" "$res"
}

@test "order: correct orders" {
    exp=$($BATS_TEST_DIRNAME/../faops filter -l 0 $BATS_TEST_DIRNAME/ufasta.fa stdout | grep -A 1 '^>read12')
    exp+=$'\n'
    exp+=$($BATS_TEST_DIRNAME/../faops filter -l 0 $BATS_TEST_DIRNAME/ufasta.fa stdout | grep -A 1 '^>read5')
    res=$($BATS_TEST_DIRNAME/../faops order -l 0 $BATS_TEST_DIRNAME/ufasta.fa <(echo read12 read5) stdout)
    assert_equal "$exp" "$res"
}

@test "order: compare with some" {
    exp=$($BATS_TEST_DIRNAME/../faops order $BATS_TEST_DIRNAME/ufasta.fa \
            <($BATS_TEST_DIRNAME/../faops size $BATS_TEST_DIRNAME/ufasta.fa | sort -n -r -k2,2 | cut -f 1) \
            stdout )

    res=$( for word in $($BATS_TEST_DIRNAME/../faops size $BATS_TEST_DIRNAME/ufasta.fa | sort -n -r -k2,2 | cut -f 1); do
            $BATS_TEST_DIRNAME/../faops some $BATS_TEST_DIRNAME/ufasta.fa <(echo ${word}) stdout
        done )

    assert_equal "$exp" "$res"
}
```
还原后：
```
#选取read12并写在一行
faops filter -l 0 ufasta.fa stdout | grep -A 1 '^>read12'
faops order -l 0 ufasta.fa <(echo read12) stdout

#选取read12和read5    -e（expression，grep中指定多个匹配模式）
grep -A 1 -e '^>read12' -e '^>read5 <(faops filter -l 0 ufasta.fa stdout)
faops order -l 0 ufasta.fa <(echo read12 read5) stdout

#按照序列大小从大到小排列后重新输出
faops order ufasta.fa \
    <(faops size ufasta.fa | sort -n -r -k2,2 | cut -f 1) \
    stdout
for word in $(faops size ufasta.fa | sort -n -r -k2,2 | cut -f 1); do
    faops some ufasta.fa <(echo ${word}) stdout
done