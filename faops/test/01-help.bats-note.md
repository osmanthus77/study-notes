# 还原为命令
```
#!/usr/bin/env bats

load test_helper

@test "run faops" {
    run $BATS_TEST_DIRNAME/../faops help
    assert_success
}

@test "help: contents" {
    run $BATS_TEST_DIRNAME/../faops help
    echo "${output}" | grep "Usage"
    assert_success
}

# xargs trims whitespaces
@test "help: lines of contents" {
    run $BATS_TEST_DIRNAME/../faops help
    run bash -c "echo \"${output}\" | wc -l | xargs echo "
    assert_equal "27" "${output}"
}
```
还原后：
```
faops help

faops help | grep "Usage"

faops help | wc -l | xargs echo
```
`faops help | wc -l | xargs echo`运行faops help、计算其输出的行数、打印结果
`wc -l`中，`wc`命令为word count统计字数/字符数/行数。`-l`为line count统计行数    
`xargs echo`中，`xargs`命令为execute arguments执行参数，从标准输入读取数据并将其作为参数传递给另一个命令，在这里是`echo`