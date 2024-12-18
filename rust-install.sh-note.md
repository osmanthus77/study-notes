```
#!/bin/bash

BASE_DIR=$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )

cd "${BASE_DIR}" || exit

mkdir -p $HOME/.cargo

if grep -q -i RUST_PATH $HOME/.bashrc; then
    echo "==> .bashrc already contains RUST_PATH"
else
    echo "==> Updating .bashrc with RUST_PATH..."
    RUST_PATH="export PATH=\"\$HOME/.cargo/bin:\$PATH\""
    echo '# RUST_PATH'       >> $HOME/.bashrc
    echo $RUST_PATH          >> $HOME/.bashrc
    echo >> $HOME/.bashrc

    # make the above environment variables available for the rest of this script
    eval $RUST_PATH
fi

echo "==> Install rustup"
curl https://sh.rustup.rs -sSf | bash -s -- -y

rustup component add clippy rust-analysis rust-src rustfmt

# This cargo mirror can't release crates
#tee $HOME/.cargo/config <<EOF
#[source.crates-io]
#registry = "https://github.com/rust-lang/crates.io-index"
#replace-with = 'ustc'
#[source.ustc]
#registry = "git://ipv4.mirrors.ustc.edu.cn/crates.io-index"
#
#EOF

cargo install cargo-expand
cargo install cargo-release
```
解释：
`curl https://sh.rustup.rs -sSf`中，    
`-s`（silent），`-S`（show error）确保silent下发生错误能输出信息，`-f`（fail silent）失败时不显示输出停止执行后续步骤    

`bash -s -- -y`中，`-s`（read commands from standard input）标准输入读取命令     
`--`参数分隔符（指示选项结束，后面的参数不是bash的选项而是传递给脚本的参数），`-y`传递给脚本的参数（yes自动确认回答）    

`rustup component add clippy rust-analysis rust-src rustfmt`中，   
`rustup`命令（官方工具链管理器），`component add`子命令（安装额外的组件）
`clippy`静态分析rust代码，`rust-analysis`分析支持rust代码（代码补全、代码重构等等），`rust-src`源代码组件，`rustfmt`代码格式化    

`tee`linux/unix命令（从stdin读取数据并写入到指定文件和stdout）