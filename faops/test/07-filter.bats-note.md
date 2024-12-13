# 还原为命令
```
@test "filter: as formatter, sequence in one line" {
    exp=$($BATS_TEST_DIRNAME/../faops size $BATS_TEST_DIRNAME/ufasta.fa | wc -l | xargs echo)
    res=$($BATS_TEST_DIRNAME/../faops filter -l 0 $BATS_TEST_DIRNAME/ufasta.fa stdout | wc -l | xargs echo)
    assert_equal "$(( exp * 2 ))" "$res"
}
```
```
faops size ufasta.fa | wc -l | xargs echo
faops filter -l 0 ufasta.fa stdout | wc -l |xargs echo
```
filter把每个序列分别写成一行     
```
@test "filter: as formatter, blocked fasta files" {
    exp=$($BATS_TEST_DIRNAME/../faops size $BATS_TEST_DIRNAME/ufasta.fa | wc -l | xargs echo)
    res=$($BATS_TEST_DIRNAME/../faops filter -b $BATS_TEST_DIRNAME/ufasta.fa stdout | wc -l | xargs echo)
    assert_equal "$(( exp * 3 ))" "$res"
}
```
```
faops size ufasta.fa | wc -l | xargs echo
faops filter -b ufasta.fa stdout | wc -l | xargs echo
```

```
@test "filter: as formatter, identical headers" {
    exp=$(grep '^>' $BATS_TEST_DIRNAME/ufasta.fa)
    res=$($BATS_TEST_DIRNAME/../faops filter -l 0 $BATS_TEST_DIRNAME/ufasta.fa stdout | grep '^>')
    assert_equal "$exp" "$res"
}
```
```
grep '^>' ufasta.fa
faops filter -l 0 ufasta.fa stdout | grep '^>'
```
每条序列分别写成一行并只提取标题行
```
@test "filter: as formatter, identical sequences" {
    exp=$(grep -v '^>' $BATS_TEST_DIRNAME/ufasta.fa | perl -ne 'chomp; print')
    res=$($BATS_TEST_DIRNAME/../faops filter -l 0 $BATS_TEST_DIRNAME/ufasta.fa stdout | grep -v '^>' | perl -ne 'chomp; print')
    assert_equal "$exp" "$res"
}
```
```
grep -v '^>' ufasta.fa | perl -ne 'chomp; print'
faops filter -l 0 ufasta.fa stdout | grep -v '^>' | perl -ne 'chomp; print'
```
`chomp`命令：去掉当前行的末尾换行符（\n或\r\n），作用对象是当前读取的那一行。eg."hello\n"经chomp变"Hello"     
全部序列连在一起输出   
```
@test "filter: as formatter, identical sequences (gz)" {
    exp=$(grep -v '^>' $BATS_TEST_DIRNAME/ufasta.fa | perl -ne 'chomp; print')
    res=$($BATS_TEST_DIRNAME/../faops filter -l 0 $BATS_TEST_DIRNAME/ufasta.fa.gz stdout | grep -v '^>' | perl -ne 'chomp; print')
    assert_equal "$exp" "$res"
}
```
```
grep -v '^>' ufasta.fa | perl -ne 'chomp; print'      #全部序列的碱基连在一起输出
faops filter -l 0 ufasta.fa  ufasta.fa stdout | grep -v '^>' | perl 'chomp; print'
```

```
@test "filter: as formatter, identical sequences (gz) with -N" {
    exp=$(grep -v '^>' $BATS_TEST_DIRNAME/ufasta.fa | perl -ne 'chomp; print')
    res=$($BATS_TEST_DIRNAME/../faops filter -l 0 -N $BATS_TEST_DIRNAME/ufasta.fa.gz stdout | grep -v '^>' | perl -ne 'chomp; print')
    assert_equal "$exp" "$res"
}
```
```
grep -v '^>' ufasta.fa | perl -ne 'chomp; print'
faops filter -l 0 -N ufasta.fa stdout | grep -v '^>' | perl -ne 'chomp; print'
```
全部序列连在一起输出并用N碱基替代模糊碱基
```
@test "filter: convert IUPAC to N" {
    exp=$(printf ">read\n%s\n" ANNG)
    res=$($BATS_TEST_DIRNAME/../faops filter -l 0 -N <(printf ">read\n%s\n" AMRG) stdout)
    assert_equal "$exp" "$res"
}
```
```
printf ">read\n%s\n" ANNG
faops filter -l 0 -N <(printf ">read\n%s\n" AMRG) stdout
```
`printf ">read\n%s\n" ANNG`模拟生成fasta格式内容的标准输出。
其中，`printf`命令（格式化输出），`%s`表示替换为后续提供的参数内容，`ANNG`是%s的参数内容。    

```
@test "filter: remove dashes" {
    exp=$(printf ">read\n%s\n" ARG)
    res=$($BATS_TEST_DIRNAME/../faops filter -l 0 -d <(printf ">read\n%s\n" A-RG) stdout)
    assert_equal "$exp" "$res"
}
```
```
printf ">read"\n%s\n" ARG
faops filter -l 0 -d <(printf ">read\n%s\n" A-RG) stdout
```
`-d`选项表示删除序列中非标准碱基

```
@test "filter: Upper cases" {
    exp=$(printf ">read\n%s\n" ATCG)
    res=$($BATS_TEST_DIRNAME/../faops filter -l 0 -U <(printf ">read\n%s\n" AtcG) stdout)
    assert_equal "$exp" "$res"
}
```
```
printf ">read\n%s\n" ATCG
faops filter -l 0 U <(printf ">read\n%s\n" AtcG) stdout
```
碱基全部转换为大写    

```
@test "filter: simplify seq names" {
    exp=$(printf ">read\n%s\n" ANNG)
    res=$($BATS_TEST_DIRNAME/../faops filter -l 0 -s <(printf ">read.1\n%s\n" ANNG) stdout)
    assert_equal "$exp" "$res"
}
```
```
printf ">read\n%s\n" ANNG
faops filter -l 0 -s <(printf ">read.1\n%s\n" ANNG) stdout
```
简化序列名称    

```
@test "filter: fastq to fasta" {
    run $BATS_TEST_DIRNAME/../faops filter $BATS_TEST_DIRNAME/test.seq stdout
    run bash -c "echo \"${output}\" | wc -l | xargs echo "
    assert_equal 6 "${output}"
}
```
```
faops filter test.seq stdout | wc -l | xargs echo
```
转换格式为fasta    

```
@test "faFilter: minSize" {
    if ! hash faFilter 2>/dev/null ; then
        skip "Can't find faFilter"
    fi

    exp=$(faFilter -minSize=10 $BATS_TEST_DIRNAME/ufasta.fa stdout | grep '^>')
    res=$($BATS_TEST_DIRNAME/../faops filter -a 10 $BATS_TEST_DIRNAME/ufasta.fa stdout | grep '^>')
    assert_equal "$exp" "$res"
}
```
```
faFilter -minSize=10 ufasta.fa stdout | grep '^>'
faops filter -a 10 ufasta.fa stdout | grep '^>'
```
选取序列长度10以上的序列   

```
@test "faFilter: maxSize" {
    if ! hash faFilter 2>/dev/null ; then
        skip "Can't find faFilter"
    fi

    exp=$(faFilter -maxSize=50 $BATS_TEST_DIRNAME/ufasta.fa stdout | grep '^>')
    res=$($BATS_TEST_DIRNAME/../faops filter -a 1 -z 50 $BATS_TEST_DIRNAME/ufasta.fa stdout | grep '^>')
    assert_equal "$exp" "$res"
}
```
```
faFilter -maxSize=50 ufasta.fa stdout | grep '^>'
faops filter -a 1 -z 50 ufasta.fa stdout | grep '^>'
```
选取序列长度1到50的序列    

```
@test "faFilter: minSize maxSize" {
    if ! hash faFilter 2>/dev/null ; then
        skip "Can't find faFilter"
    fi

    exp=$(faFilter -minSize=10 -maxSize=50 $BATS_TEST_DIRNAME/ufasta.fa stdout | grep '^>')
    res=$($BATS_TEST_DIRNAME/../faops filter -a 10 -z 50 $BATS_TEST_DIRNAME/ufasta.fa stdout | grep '^>')
    assert_equal "$exp" "$res"
}
```
```
faFilter -minSize=10 -maxSize=50 ufasta.fa stdout | grep '^>'
faops filter -a 10 -z 50 ufasta.fa stdout | grep '^>'
```
选取序列长度10-50的序列    

```
@test "faFilter: uniq" {
    if ! hash faFilter 2>/dev/null ; then
        skip "Can't find faFilter"
    fi

    exp=$(faFilter -uniq <(cat $BATS_TEST_DIRNAME/ufasta.fa $BATS_TEST_DIRNAME/ufasta.fa) stdout | grep '^>')
    res=$($BATS_TEST_DIRNAME/../faops filter -u -a 1 <(cat $BATS_TEST_DIRNAME/ufasta.fa $BATS_TEST_DIRNAME/ufasta.fa) stdout | grep '^>')
    assert_equal "$exp" "$res"
}
```
```
faFilter -uniq <(cat ufasta.fa) stdout | grep '^>'
faops filter -u -a 1 <(cat ufasta.fa) stdout | grep '^>'
```
`-uniq`、`-u`（unique）删除重复序列只保留第一条   