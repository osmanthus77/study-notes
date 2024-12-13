# 还原为命令
```
#!/usr/bin/env bats

load test_helper

@test "one: inline names" {
    exp=$($BATS_TEST_DIRNAME/../faops filter -l 0 $BATS_TEST_DIRNAME/ufasta.fa stdout | grep -A 1 '^>read12')
    res=$($BATS_TEST_DIRNAME/../faops one -l 0 $BATS_TEST_DIRNAME/ufasta.fa read12 stdout)
    assert_equal "$exp" "$res"
}

@test "faSomeRecords: inline names" {
    if ! hash faSomeRecords 2>/dev/null ; then
        skip "Can't find faSomeRecords"
    fi

    exp=$(faSomeRecords $BATS_TEST_DIRNAME/ufasta.fa <(echo read12) stdout | grep '^>')
    res=$($BATS_TEST_DIRNAME/../faops one $BATS_TEST_DIRNAME/ufasta.fa read12 stdout | grep '^>')
    assert_equal "$exp" "$res"
}
```
还原后：
```
faops filter -l 0 ufasta.fa stdout | grep -A 1 '^>read12'
faops one -l 0 ufasta.fa read12 stdout

faSomeRecords ufasta.fa <(echo read12) stdout | grep '^>'
faops one ufasta.fa read12 stdout | grep '^>'
```
`grep -A 1 '^read12'`中，`-A 数字`（--after-context）匹配行后面的n（数字）行
ps：`-B 数字`（before）匹配行前面的n行。`-C 数字`（context）匹配行前后各n行。
`-v`（--invert-match）不匹配指定模式的行。`-i`（--ignore-case）忽略大小写。