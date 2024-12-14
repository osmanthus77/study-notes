# 还原为命令
```
#!/usr/bin/env bats

load test_helper

# create replace.tsv
# faops size test/ufasta.fa | perl -nl -e "/\s+0$/ or print" > test/replace.tsv

# ./faops replace -l 0 test/ufasta.fa <(printf "%s\t%s\n" read12 428) stdout

# ./faops replace -s test/ufasta.fa <(printf "%s\t%s\n" read12 428) stdout | wc -l | xargs

# ./faops replace -l 0 test/ufasta.fa test/replace.tsv stdout

@test "replace: inline names" {
    exp=">428"
    res=$($BATS_TEST_DIRNAME/../faops replace $BATS_TEST_DIRNAME/ufasta.fa \
        <(printf "%s\t%s\n" read12 428) stdout \
        | grep '^>428')
    assert_equal "$exp" "$res"
}

@test "replace: -s" {
    exp="7"
    res=$($BATS_TEST_DIRNAME/../faops replace -s $BATS_TEST_DIRNAME/ufasta.fa \
        <(printf "%s\t%s\n" read12 428) stdout |
        wc -l |
        xargs echo)
    assert_equal "$exp" "$res"
}

@test "replace: with replace.tsv" {
    exp=$(cat $BATS_TEST_DIRNAME/replace.tsv | cut -f 2)
    res=$($BATS_TEST_DIRNAME/../faops replace $BATS_TEST_DIRNAME/ufasta.fa \
        $BATS_TEST_DIRNAME/replace.tsv stdout \
        | grep '^>' | grep -v 'read' | sed 's/>//' )
    assert_equal "$exp" "$res"
}
```
还原后：
```
#读取fasta中每一行，将不是以空格和0结尾的行（即序列长度为0的）输出到replace.tsv中
touch replace.tsv
faops size ufasta.fa | perl -nl -e "/\s+0$/ or print" > replace.tsv


faops replace ufasta.fa <(printf "%s\t%s\n" read12 428) stdout | grep '^>428'

#只输出列表中替换的序列内容再输出行数
faops replace -s ufasta.fa <(printf "%s\t%s\n" read12 428) stdout | wc -l | xargs echo

#用replace.tsv文件给fasta替换序列名称，筛选出序列名称、去掉名称里含有read的，再去掉>
cat replace.tsv | cut -f 2
faops replace ufasta.fa replace.tsv stdout | grep '^>' | grep -v 'read' | sed 's/>//'
```
解释1:   
`perl -nl -e "/\s+0$/ or print"`中，`-l`（--line-ending）perl处理每一行时自动去除行尾的换行符，并在输出时自动加上换行符   
`/\s+0$/`正则表达式，匹配内容为（以空白字符跟着数字0，且0为行的末尾）
`\s`匹配任意空白字符（空格、制表符、换行符、回车符等），`+`表示匹配一个或多个前面的字符（\s）。    
`0`匹配字符0，`$`正则表达式中一个锚点（表示行尾），确保匹配的部分（这里是0）出现在行的末尾    
`or print`匹配不到就print     

解释2:    
`<(printf "%s\t%s\n" read12 428)`replace的替换规则（read12\t428\n）用428替换read12
`%s`表示替换为后续提供的参数内容，`\t`一个制表符，`\n`换行符，`read12`替代第一个%s，`428`替代第二个%s    
