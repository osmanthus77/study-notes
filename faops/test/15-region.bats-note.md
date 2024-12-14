# 还原为命令
```
#!/usr/bin/env bats

load test_helper

@test "region: from file" {
    exp=$(cat $BATS_TEST_DIRNAME/region.txt | wc -l | xargs echo)
    res=$($BATS_TEST_DIRNAME/../faops region -l 0 $BATS_TEST_DIRNAME/ufasta.fa $BATS_TEST_DIRNAME/region.txt stdout | wc -l | xargs echo)
    assert_equal "$(($exp * 2))" "$res"
}

@test "region: frag" {
    exp=$($BATS_TEST_DIRNAME/../faops frag $BATS_TEST_DIRNAME/ufasta.fa 1 10 stdout)
    res=$($BATS_TEST_DIRNAME/../faops region -l 0 $BATS_TEST_DIRNAME/ufasta.fa <(echo read0:1-10) stdout)
    assert_equal "${exp}" "${res}"
}

@test "region: 1 base" {
    exp=$($BATS_TEST_DIRNAME/../faops frag $BATS_TEST_DIRNAME/ufasta.fa 10 10 stdout)
    res=$($BATS_TEST_DIRNAME/../faops region -l 0 $BATS_TEST_DIRNAME/ufasta.fa <(echo read0:10) stdout)
    assert_equal "${exp}" "${res}"
}

@test "region: strand" {
    exp=$(echo -e ">read0(+):10\nC")
    res=$($BATS_TEST_DIRNAME/../faops region -s -l 0 $BATS_TEST_DIRNAME/ufasta.fa <(echo read0:10) stdout)
    assert_equal "${exp}" "${res}"
}

@test "region: regions" {
    exp=4
    res=$($BATS_TEST_DIRNAME/../faops region -l 0 $BATS_TEST_DIRNAME/ufasta.fa <(echo read1:1-10,50-60) stdout | wc -l | xargs echo)
    assert_equal "${exp}" "${res}"
}
```
还原后：
```
#根据region.txt提取指定序列中指定片段，输出单行，计行数
cat region.txt | wc -l | xargs echo
faops region -l 0 ufasta.fa region.txt stdout | wc -l | xargs echo

#提取read0的1到10，跟frag得到相同效果
faops frag ufasta.fa 1 10 stdout
faops region -l 0 ufasta.fa <(echo read0:1-10) stdout

#提取read0第10号位置碱基
faops frag ufasta.fa 10 10 stdout
faops region -l 0 ufasta.fa <(echo read0:10) stdout

#提取read0第10号位置碱基并显示方向
echo -e ">read0(+):10\nC"
faops region -s -l 0 ufasta.fa <(echo read0:10) stdout

#提取read1的1-10和50-60，统计行数
faops region -l 0 ufasta.fa <(echo read1:1-10,50-60) stdout | wc -l | xargs echo
```