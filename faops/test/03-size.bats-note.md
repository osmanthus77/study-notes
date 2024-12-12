# 还原为命令
```
#!/usr/bin/env bats

load test_helper

@test "size: read from file" {
    run bash -c "$BATS_TEST_DIRNAME/../faops size $BATS_TEST_DIRNAME/ufasta.fa | head -n 2"
    assert_equal "read0${tab}359" "${lines[0]}"
    assert_equal "read1${tab}106" "${lines[1]}"
}

@test "size: read from gzipped file" {
    run bash -c "$BATS_TEST_DIRNAME/../faops size $BATS_TEST_DIRNAME/ufasta.fa.gz | head -n 2"
    assert_equal "read0${tab}359" "${lines[0]}"
    assert_equal "read1${tab}106" "${lines[1]}"
}

@test "size: read from stdin" {
    run bash -c "cat $BATS_TEST_DIRNAME/ufasta.fa | $BATS_TEST_DIRNAME/../faops size stdin | head -n 2"
    assert_equal "read0${tab}359" "${lines[0]}"
    assert_equal "read1${tab}106" "${lines[1]}"
}

@test "size: lines of result" {
    run $BATS_TEST_DIRNAME/../faops size $BATS_TEST_DIRNAME/ufasta.fa
    run bash -c "echo \"${output}\" | wc -l | xargs echo "
    assert_equal 50 "${output}"
}

@test "size: mixture of stdin and actual file" {
    run bash -c "cat $BATS_TEST_DIRNAME/ufasta.fa | $BATS_TEST_DIRNAME/../faops size stdin $BATS_TEST_DIRNAME/ufasta.fa"
    run bash -c "echo \"${output}\" | wc -l | xargs echo "
    assert_equal 100 "${output}"
}

@test "size: sum of sizes" {
    run bash -c "
        $BATS_TEST_DIRNAME/../faops size $BATS_TEST_DIRNAME/ufasta.fa \
            | perl -ane '\$c += \$F[1]; END { print qq{\$c\n} }'
    "
    assert_equal 9317 "${output}"
}
```
还原后：
```
faops size ufasta.fa | head -n 2

faops size ufasta.fa.gz | head -n 2

cat ufasta.fa | faops size stdi | head -n 2

faops size ufasta.fa | wc -l | xargs echo

cat ufasta.fa | faops size stdin ufasta.fa | wc -l | xargs echo

faops size ufasta.fa.gz | perl -ane '\$c += \$F[1]; END { print qq{\$c\n} }'
```
`perl -ane '\$c += \$F[1]; END { print qq{\$c\n} }'`累加每行第二个字段并最后输出总和       
`\$c += \$F[1]`中，`\$F[1]`引用$F[1]数组元素（没有\为直接访问），`+=`perl中加法赋值操作（将$F[1]加到变量$c中，逐行累加）    
`END { print qq{\$c\n} }`中，`END{...}`perl中END块（处理完所有输入数据后再执行END中代码）