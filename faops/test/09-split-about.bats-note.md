# 还原为命令
```
#!/usr/bin/env bats

load test_helper

@test "split-about: 2000 bp " {
    mytmpdir=`mktemp -d 2>/dev/null || mktemp -d -t 'mytmpdir'`

    run bash -c "
        $BATS_TEST_DIRNAME/../faops split-about $BATS_TEST_DIRNAME/ufasta.fa 2000 $mytmpdir \
        && find $mytmpdir -name '*.fa' | wc -l | xargs echo
    "
    assert_equal 5 "${output}"

    rm -fr ${mytmpdir}
}

@test "split-about: max parts" {
    mytmpdir=`mktemp -d 2>/dev/null || mktemp -d -t 'mytmpdir'`

    run bash -c "
        $BATS_TEST_DIRNAME/../faops split-about -m 2 $BATS_TEST_DIRNAME/ufasta.fa 2000 $mytmpdir \
        && find $mytmpdir -name '*.fa' | wc -l | xargs echo
    "
    assert_equal 2 "${output}"

    rm -fr ${mytmpdir}
}

@test "split-about: 2000 bp and size restrict" {
    mytmpdir=`mktemp -d 2>/dev/null || mktemp -d -t 'mytmpdir'`

    run bash -c "
        $BATS_TEST_DIRNAME/../faops filter -a 100 $BATS_TEST_DIRNAME/ufasta.fa stdout \
        | $BATS_TEST_DIRNAME/../faops split-about stdin 2000 $mytmpdir \
        && find $mytmpdir -name '*.fa' | wc -l | xargs echo
    "
    assert_equal 4 "${output}"

    rm -fr ${mytmpdir}
}

@test "split-about: 1 bp" {
    mytmpdir=`mktemp -d 2>/dev/null || mktemp -d -t 'mytmpdir'`

    run bash -c "
        $BATS_TEST_DIRNAME/../faops split-about $BATS_TEST_DIRNAME/ufasta.fa 1 $mytmpdir \
        && find $mytmpdir -name '*.fa' | wc -l | xargs echo
    "
    assert_equal 50 "${output}"

    rm -fr ${mytmpdir}
}

@test "split-about: 1 bp even" {
    mytmpdir=`mktemp -d 2>/dev/null || mktemp -d -t 'mytmpdir'`

    run bash -c "
        $BATS_TEST_DIRNAME/../faops split-about -e $BATS_TEST_DIRNAME/ufasta.fa 1 $mytmpdir \
        && find $mytmpdir -name '*.fa' | wc -l | xargs echo
    "
    assert_equal 26 "${output}"

    rm -fr ${mytmpdir}
}
```
还原后：
```
#按照2000bp左右拆分fasta并查询拆分后文件数量
mytmpdir=`mktemp -d 2>/dev/null || mktemp -d -t 'mytmpdir'`
faops split-about ufasta.fa 2000 $mytmpdir && find $mytmpdir -name '*.fa' | wc -l | xargs echo

#按照2000bp左右拆分fasta，最多拆分为两个文件，并查询拆分后文件数量
faops split-about -m 2 ufasta.fa 2000 $mytmpdir && find $mytmpdir -name '*.fa' | wc -l | xargs echo

#筛选出大于100的序列后，按照2000bp拆分，并查询拆分后文件数量
faops filter -a 100 ufasta.fa stdout \
| faops split-about stdin 2000 $mytmpdir \
&& find $mytmpdir -name '*.fa' | wc -l | xargs echo

#按照1bp拆分fasta后查询拆分文件数量    （每个序列都拆分成文件）
faops split-about ufasta.fa 1 $mytmpdir && find $mytmpdir -name '*.fa' | wc -l | xargs echo

#按照1bp平均拆分序列数量（拆分后每个文件中序列条数平均）
faops split-about -e ufasta.fa 1 $mytmpdir && find $mytmpdir -name '*.fa' | wc -l | xargs echo
```