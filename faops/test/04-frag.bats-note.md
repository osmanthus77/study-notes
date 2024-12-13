# 还原为命令
```
#!/usr/bin/env bats

load test_helper

@test "frag: from first sequence" {
    res=$($BATS_TEST_DIRNAME/../faops frag $BATS_TEST_DIRNAME/ufasta.fa 1 10 stdout | grep -v "^>")
    assert_equal "tCGTTTAACC" "${res}"
}

@test "frag: from specified sequence" {
    res=$($BATS_TEST_DIRNAME/../faops some $BATS_TEST_DIRNAME/ufasta.fa <(echo read12) stdout \
        | $BATS_TEST_DIRNAME/../faops frag stdin 1 10 stdout \
        | grep -v "^>")
    assert_equal "AGCgCcccaa" "${res}"
}
```
还原后：
```
faops frag ufasta.fa 1 10 stdout | grep -v "^>"

faops some ufastfa.fa <(echo read12) stdout \
    | faops frag stdin 1 10 stdout \
    |grep -v "^>"
```
`grep -v "^>"`表示排除掉所有以>开头的行。 `-v`(invert match)告诉grep反转匹配（输出不包含匹配模式的行），`"^>"`正则表达式（以>开头的行）