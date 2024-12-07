#!/bin/bash

BASE_DIR=$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )    #BASE_DIR（储存某个路径，特别是脚本中存储脚本文件所在目录路径）。
#BASH_SOURCE[0]表示，当前正在执行的脚本文件的路径，[0]当前脚本本身。dirname命令（返回指定路径的目录部分，如/usr/script.sh返回/usr）。
#cd切换到脚本所在目录。pwd命令（print working directory）打印目录

cd "${BASE_DIR}" || exit   #切换到base_dir赋值的目录，失败则退出。避免继续执行错误命令。

hash cpanm 2>/dev/null || {
    curl -L https://cpanmin.us | perl - App::cpanminus
}

CPAN_MIRROR=https://mirrors.ustc.edu.cn/CPAN/
NO_TEST=--notest    #NO_TEST变量（存储信息），--notest禁用测试功能。

# basic modules
cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST Archive::Extract Config::Tiny File::Find::Rule Getopt::Long::Descriptive JSON JSON::XS Text::CSV_XS YAML::Syck
#Archive::Extract模块（解压归档文件），Config::Tiny（INI文件解析器），File::Find::Rule（递归查找文件），Getopt::Long::Descriptive（扩展Getopt::Long，简化命令行选项的解析）
#JSON（json格式的编解码器），JSON::XS（比JSON更快），Text::CSV（快速处理csv文件），YAML::Syck（解析和生成YAML格式）
cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST App::Ack App::Cmd DBI MCE Moo Moose Perl::Tidy Template WWW::Mechanize XML::Parser
#App::Ack快速在代码库进行文本搜索，App::Cmd构建perl命令行，DBI（database interfere）perl中用于与数据库交互，MCE（Multi-Processing Engine）并行处理，Moo面向对象编程
#Moose面向对象编程框架（更复杂），Perl::Tidy（脚本格式化），Template生成动态内容、网页开发，WWW::Mechanize自动化网页交互，XML::Parser解析处理xml文件

# RepeatMasker need this
cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST Text::Soundex   #帮助比较发音相似的单词或名字，常用于数据清理、去重和匹配等。Text::Soundex计算和处理soundex编码，soundex将单词转为编码。

# GD
cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST GD SVG GD::SVG   #GD图形库，SVG矢量图，GD::SVG利用gd绘制矢量图并输出svg格式

# bioperl
cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST Data::Stag Test::Most URI::Escape Algorithm::Munkres Array::Compare Clone Error File::Sort Graph List::MoreUtils Set::Scalar Sort::Naturally
cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST HTML::Entities HTML::HeadParser HTML::TableExtract HTTP::Request::Common LWP::UserAgent PostScript::TextBlock
cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST XML::DOM XML::DOM::XPath XML::SAX::Writer XML::Simple XML::Twig XML::Writer GraphViz SVG::Graph
cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST SHLOMIF/XML-LibXML-2.0134.tar.gz
cpanm --mirror-only --mirror $CPAN_MIRROR --notest Convert::Binary::C IO::Scalar
cpanm --mirror-only --mirror $CPAN_MIRROR --notest CJFIELDS/BioPerl-1.007002.tar.gz

cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST Bio::ASN1::EntrezGene Bio::DB::EUtilities Bio::Graphics
cpanm --mirror-only --mirror $CPAN_MIRROR --notest CJFIELDS/BioPerl-Run-1.007002.tar.gz # BioPerl-Run

# circos
cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST Config::General Data::Dumper Digest::MD5 Font::TTF::Font Math::Bezier Math::BigFloat Math::Round Math::VecStat
cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST Params::Validate Readonly Regexp::Common Set::IntSpan Statistics::Basic Text::Balanced Text::Format Time::HiRes

# Bio::Phylo
cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST XML::XML2JSON PDF::API2 Math::CDF Math::Random
cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST Bio::Phylo

# Database and WWW
cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST MongoDB LWP::Protocol::https Mojolicious

# text, rtf and xlsx
cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST Roman Text::Table RTF::Writer Chart::Math::Axis
cpanm --mirror-only --mirror $CPAN_MIRROR --notest Excel::Writer::XLSX Spreadsheet::XLSX Spreadsheet::ParseExcel Spreadsheet::WriteExcel

# Test::*
cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST Test::Class Test::Roo Test::Taint Test::Without::Module

# Moose and Moo
cpanm --mirror-only --mirror $CPAN_MIRROR --notest MooX::Options MooseX::Storage

# Develop
cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST App::pmuninstall App::cpanoutdated Minilla Version::Next CPAN::Uploader

# Others
cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST DateTime::Format::Natural DBD::CSV String::Compare Sereal PerlIO::gzip

# AlignDB::*
cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST AlignDB::IntSpan AlignDB::Stopwatch
cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST AlignDB::Codon AlignDB::DeltaG AlignDB::GC AlignDB::SQL AlignDB::Window AlignDB::ToXLSX
cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST App::RL App::Fasops App::Rangeops

# App::*
cpanm -nq https://github.com/wang-q/App-Plotr.git
cpanm -nq https://github.com/wang-q/App-Egaz.git

# Gtk3 stuffs
# cpanm --mirror-only --mirror $CPAN_MIRROR $NO_TEST Glib Cairo Cairo::GObject Glib::Object::Introspection Gtk3 Pango

# Math
# cpanm --mirror-only --mirror $CPAN_MIRROR --notest Math::Random::MT::Auto PDL Math::GSL

# Statistics::R would be installed in `brew.sh`
# DBD::mysql would be installed in `mysql8.sh`
