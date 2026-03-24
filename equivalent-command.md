# `dce-linux-mptcp-kernel4.4` 构建命令说明

本文基于以下前提：

- 当前目录是 `/root/bake`
- 执行的是 `./bake.py -V configure -e dce-linux-mptcp-kernel4.4`
- 然后执行 `./bake.py build`
- 没有额外传入 `--sourcedir`、`--installdir`、`-j`、`-p`

按 `bake/Bake.py` 的默认值，这意味着：

- 源码目录是 `/root/bake/source`
- 安装目录是 `/root/bake/build`
- `./bake.py` 通过 `python3` 运行
- 没有 `-j` 时，Bake 不会给 `waf` 或 `make` 追加并行参数

这里“build ns-3”对应模块 `ns-3.35`，“build ns-3-dce”对应真正执行编译的模块 `dce-linux-mptcp-kernel4.4`。`dce-meta-mptcp-kernel4.4` 只是依赖聚合模块，`build type="none"`，本身不执行编译命令。

## 1. build ns-3 的命令

`bakeconf.xml` 中 `ns-3.35` 的构建方式是 `waf`，配置参数为：

`configure --prefix=$INSTALLDIR --enable-examples --enable-tests`

因此，Bake 对应执行的等价命令是：

```bash
cd /root/bake/source/ns-3.35
python3 ./waf configure --prefix=/root/bake/build --enable-examples --enable-tests
python3 ./waf
python3 ./waf install
```

## 2. build ns-3-dce 的命令

这里对应的是模块 `dce-linux-mptcp-kernel4.4`。它的源码目录是 `ns-3-dce`，构建方式是 `waf`，配置参数为：

`configure --prefix=$INSTALLDIR --with-ns3=$INSTALLDIR --with-glibc=$INSTALLDIR/glibc --with-libaspect=$INSTALLDIR --enable-kernel-stack=$SRCDIR/../net-next-nuse-mptcp-0.92/arch`

把变量展开后，Bake 对应执行的等价命令是：

```bash
cd /root/bake/source/ns-3-dce
python3 ./waf configure --prefix=/root/bake/build --with-ns3=/root/bake/build --with-glibc=/root/bake/build/glibc --with-libaspect=/root/bake/build --enable-kernel-stack=/root/bake/source/net-next-nuse-mptcp-0.92/arch
python3 ./waf
python3 ./waf install
```

## 3. build net-next-nuse-mptcp-0.92 的命令

`net-next-nuse-mptcp-0.92` 的构建方式是 `make`。它有：

- `configure_arguments="defconfig ARCH=lib"`
- `build_arguments="library ARCH=lib"`
- `no_installation="True"`
- 一个 `post_installation` 命令

所以 Bake 对应执行的等价命令是：

```bash
cd /root/bake/source/net-next-nuse-mptcp-0.92
make defconfig ARCH=lib
make library ARCH=lib
```

这个模块不会执行 `make install`。但 Bake 仍会在构建后执行 `post_installation`，等价于：

```bash
mkdir -p /root/bake/build/bin_dce
cd /root/bake/build/bin_dce
cp /root/bake/source/net-next-nuse-mptcp-0.92/arch/lib/tools/libsim-linux-4.4.110.so ./
ln -s -f libsim-linux-4.4.110.so liblinux.so
```

## 4. 补充说明

虽然 `dce-linux-mptcp-kernel4.4` 的 XML 里写了 `objdir="objdir"` 和 `sourcedir="ns-3-dce"`，但当前仓库里的 `WafModuleBuild` 实现并不会把它们转换成 `waf --out=...` 之类的命令行参数。实际工作目录仍然按模块源码目录 `/root/bake/source/ns-3-dce` 来理解。

如果你改成：

```bash
./bake.py build -j N
```

那么 Bake 只会在“真正的 build 阶段”追加并行参数：

- `ns-3.35` 变成 `python3 ./waf -j N`
- `dce-linux-mptcp-kernel4.4` 变成 `python3 ./waf -j N`
- `net-next-nuse-mptcp-0.92` 变成 `make -j N library ARCH=lib`

配置阶段仍然分别是：

- `python3 ./waf configure ...`
- `make defconfig ARCH=lib`
