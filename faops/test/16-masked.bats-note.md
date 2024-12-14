# 还原为命令
```
#!/usr/bin/env bats

load test_helper

@test "masked" {
    exp="read46:3-4"
    res=$($BATS_TEST_DIRNAME/../faops masked $BATS_TEST_DIRNAME/ufasta.fa | grep '^read46' | head -n 1)
    assert_equal "$exp" "$res"
}

@test "masked" {
    exp="read0:1"
    res=$($BATS_TEST_DIRNAME/../faops masked $BATS_TEST_DIRNAME/ufasta.fa | grep '^read0' | head -n 1)
    assert_equal "$exp" "$res"
}
```
还原后：
```
#定位冗余碱基位置，并输出read46的第一行
faops masked ufasta.fa | grep '^read46' | head -n 1

#定位冗余碱基位置，并输出read0的第一行
faops masked ufasta.fa | grep '^read0' | head -n 1
```