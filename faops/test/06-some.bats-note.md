# 还原为命令
```
#!/usr/bin/env bats

load test_helper

@test "some: inline names" {
    exp=$($BATS_TEST_DIRNAME/../faops filter -l 0 $BATS_TEST_DIRNAME/ufasta.fa stdout | grep -A 1 '^>read12')
    res=$($BATS_TEST_DIRNAME/../faops some -l 0 $BATS_TEST_DIRNAME/ufasta.fa <(echo read12) stdout)
    assert_equal "$exp" "$res"
}

@test "faSomeRecords: inline names" {
    if ! hash faSomeRecords 2>/dev/null ; then
        skip "Can't find faSomeRecords"
    fi

    exp=$(faSomeRecords $BATS_TEST_DIRNAME/ufasta.fa <(echo read12) stdout | grep '^>')
    res=$($BATS_TEST_DIRNAME/../faops some $BATS_TEST_DIRNAME/ufasta.fa <(echo read12) stdout | grep '^>')
    assert_equal "$exp" "$res"
}

@test "faSomeRecords: exclude" {
    if ! hash faSomeRecords 2>/dev/null ; then
        skip "Can't find faSomeRecords"
    fi

    exp=$(faSomeRecords -exclude $BATS_TEST_DIRNAME/ufasta.fa <(echo read12) stdout | grep '^>')
    res=$($BATS_TEST_DIRNAME/../faops some -i $BATS_TEST_DIRNAME/ufasta.fa <(echo read12) stdout | grep '^>')
    assert_equal "$exp" "$res"
}
```
还原后：
```
faops filter -l 0 ufasta.fa stdout | grep -A 1 '^>read12'
faops some -l 0 ufasta.fa <(echo read12) stdout

faSomeRecords ufasta.fa <(echo read12) stdout | grep '^>'
faops some ufasta.fa <(echo read12) stdout | grep '^>'

faSomeRecords -exclude ufasta.fa <(echo read12) | grep '^>'
faops some -i ufasta.fa <(echo read12) stdout | grep '^>'
```
`faops some -i ufasta.fa <(echo read12) stdout`反向提取除了read12以外的序列