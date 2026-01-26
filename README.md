# ns-3-mptcp-sim-bundle

This is a renew of the [direct-code-execution/ns-3-dce](https://github.com/direct-code-execution/ns-3-dce) project, adding MPTCP and 5g-NR functionality to the original project. In order to do this, we use the Linux network stack inside DCE, with the [out-of-tree MPTCP kernel v0.92 having kernel version 4.4.110](https://github.com/wonderfulnx/net-next-nuse-mptcp), along with other changes.

System requirement: Ubuntu 20.04 (vm is ok).

More information can be found on the DCE documentation (https://ns-3-dce.readthedocs.io/en/latest/).

## Quick Start

We give a quick start intro of our own. The whole framework has only been tested on an ubuntu 20.04.

First, install some required packages.
```sh
sudo apt install automake bc bison build-essential ccache cmake curl flex g++ gawk gdb gir1.2-goocanvas-2.0 gir1.2-gtk-3.0 git indent ipython3 libboost-dev libc6 libc6-dbg libc6-dev libdb-dev libeigen3-dev libelf-dev libgsl-dev libgtk-3-dev libncurses5-dev libpcap-dev libsctp-dev libsqlite3-dev libssl-dev libsysfs-dev lksctp-tools mercurial ninja-build pkg-config python3 python3-gi python3-gi-cairo python3-pip python3-pygraphviz python3-pygments python3-setuptools python3-sphinx qt5-default rsync sphinx-common sphinx-doc sqlite sqlite3 vim wget zsh
```

Download Bake project and set some environment variables:
```sh
git clone https://gitlab.com/nsnam/bake.git
cd bake
export PATH=$PATH:`pwd`/build/bin:`pwd`/build/bin_dce
export LD_LIBRARY_PATH=`pwd`/build/lib${LD_LIBRARY_PATH:+:$LD_LIBRARY_PATH}
export DCE_PATH=`pwd`/build/bin_dce:`pwd`/build/sbin
```

Download our bake configuration file to replace the original one (`-f` option of bake somehow does not work, so we avoid using it):
```sh
wget -O bakeconf.xml https://raw.githubusercontent.com/wonderfulnx/ns-3-mptcp-sim-bundle/main/dce-mptcp.xml
```

Then you can install the whole bundle with Bake, using check and show to check if all packages are fine.
```sh
./bake.py -V configure -e dce-linux-mptcp-kernel4.4
./bake.py check
./bake.py show
```

They should give the following:
```sh
# naxin @ naxin-Standard-PC-Q35-ICH9-2009 in ~/Documents/ns3sim/bake on git:master x [16:46:37]
$ ./bake.py check
 > Python3 - OK
 > GNU C++ compiler - OK
 > Git - OK
 > Tar tool - OK
 > Unzip tool - OK
 > Make - OK
 > CMake - OK
 > patch tool - OK


 > Path searched for tools: /usr/local/sbin /usr/local/bin /usr/sbin /usr/bin /sbin /bin /usr/games /usr/local/games /snap/bin /home/naxin/Documents/ns3sim/bake/source/ns-3-dce/build/bin /home/naxin/Documents/ns3sim/bake/source/ns-3-dce/build/bin_dce bin


# naxin @ naxin-Standard-PC-Q35-ICH9-2009 in ~/Documents/ns3sim/bake on git:master x [16:46:42]
$ ./bake.py show
module: mercurial (enabled)
  No dependencies!
module: bash (enabled)
  No dependencies!
module: thttpd (enabled)
  No dependencies!
module: wget (enabled)
  No dependencies!
module: iperf (enabled)
  No dependencies!
module: lksctp-dev (enabled)
  No dependencies!
module: libssl-dev (enabled)
  No dependencies!
module: flex (enabled)
  No dependencies!
module: bison (enabled)
  No dependencies!
module: libdb-dev (enabled)
  No dependencies!
module: bc (enabled)
  No dependencies!
module: sqlite3 (enabled)
  No dependencies!
module: sqlite (enabled)
  No dependencies!
module: gawk (enabled)
  No dependencies!
module: pygoocanvas (enabled)
  No dependencies!
module: gir-bindings (enabled)
  No dependencies!
module: gi-cairo (enabled)
  No dependencies!
module: pygobject (enabled)
  No dependencies!
module: pygraphviz (enabled)
  No dependencies!
module: setuptools (enabled)
  No dependencies!
module: python3-dev (enabled)
  No dependencies!
module: qt (enabled)
  No dependencies!
module: g++ (enabled)
  No dependencies!
module: glibc-2.31 (enabled)
  depends on:
     gawk (optional:False)
module: iproute-mptcp-0.92 (enabled)
  depends on:
     libdb-dev (optional:False)
     bison (optional:False)
     flex (optional:False)
     libssl-dev (optional:False)
module: net-next-nuse-mptcp-0.92 (enabled)
  depends on:
     bc (optional:False)
module: libaspect (enabled)
  depends on:
     mercurial (optional:False)
module: pyviz-gtk3-prerequisites (enabled)
  depends on:
     python3-dev (optional:True)
     pygraphviz (optional:True)
     pygobject (optional:True)
     gi-cairo (optional:True)
     gir-bindings (optional:True)
     pygoocanvas (optional:True)
module: pybindgen-0.22.0 (enabled)
  depends on:
     python3-dev (optional:True)
     setuptools (optional:False)
module: netanim-3.108 (enabled)
  depends on:
     qt (optional:False)
     g++ (optional:False)
module: linux-4.4-mptcp (enabled)
  depends on:
     glibc-2.31 (optional:False)
module ns: ns-3.35 (enabled)
  depends on:
     netanim-3.108 (optional:True)
     pybindgen-0.22.0 (optional:True)
     pyviz-gtk3-prerequisites (optional:True)
module: 5g-lena-1.2.y-ns3.35 (enabled)
  depends on:
     ns-3.35 (optional:False)
     sqlite (optional:False)
     sqlite3 (optional:False)
module: dce-meta-mptcp-kernel4.4 (enabled)
  depends on:
     5g-lena-1.2.y-ns3.35 (optional:False)
     glibc-2.31 (optional:False)
     linux-4.4-mptcp (optional:False)
     libaspect (optional:True)
     iperf (optional:True)
     wget (optional:True)
     thttpd (optional:True)
     bash (optional:True)
module: dce-linux-mptcp-kernel4.4 (enabled)
  depends on:
     dce-meta-mptcp-kernel4.4 (optional:False)
     net-next-nuse-mptcp-0.92 (optional:False)
     iproute-mptcp-0.92 (optional:False)
     lksctp-dev (optional:True)

-- System Dependencies --
 > bc - OK
 > bison - OK
 > flex - OK
 > g++ - OK
 > gawk - OK
 > gi-cairo - OK
 > gir-bindings - OK
 > libdb-dev - OK
 > libssl-dev - OK
 > lksctp-dev - OK
 > mercurial - OK
 > pygobject - OK
 > pygoocanvas - OK
 > pygraphviz - OK
 > python3-dev - OK
 > qt - OK
 > setuptools - OK
 > sqlite - OK
 > sqlite3 - OK
```

If everything is OK, proceed with: (The logs are large, but should give you all the information)
```sh
./bake.py download -vvv | tee bake_download.log
./bake.py build -vvv | tee bake_build.log
```

After building, change into the source/ns-3-dce directory and check that tests pass by running:
```sh
cd source/ns-3-dce
./test.py
```

Finally, you can run some MPTCP examples to see if it works. There are the two examples we modified and made sure that both works. The first is an Ethernet MPTCP:
```sh
NS_LOG="DceApplication=level_function|func|prefix_time" ./waf --run "dce-iperf-mptcp --help"
# 1. Run either
NS_LOG="DceApplication=level_function|func|prefix_time" ./waf --run "dce-iperf-mptcp"
#    or
NS_LOG="DceApplication=level_function|func|prefix_time" ./waf --run "dce-iperf-mptcp --disMPTCP"
#    and check the results of DCE-related apps run on nodes with
for d in files-*; do
    echo "========== $d =========="
    cat "$d"/var/log/*/cmdline
    echo
done
#    along with
for p in files-0/var/log/*; do
    pid=$(basename "clear$p")
    echo "========== PID $pid =========="
    echo "[cmdline]"
    tr '\0' ' ' < "$p/cmdline"
    echo "[stdout]"
    cat "$p/stdout"
    echo "[stderr]"
    cat "$p/stderr"
    echo
done
```

The two `for` loop here shows all cmdlines of all nodes, and all results from a specific node (files-0 here).

Another example is a LTE and Wi-Fi MPTCP:

```sh
NS_LOG="DceManager=level_function|func|prefix_time" ./waf --run "dce-mptcp-lte-wifi --help"
# Run these one by one to see the iperf result:
rm -rf files-*; rm *.pcap
NS_LOG="DceApplication=level_function|func|prefix_time" ./waf --run "dce-iperf-mptcp"
#   check results using the `for` loop
rm -rf files-*; rm *.pcap
NS_LOG="DceManager=level_function|func|prefix_time" ./waf --run "dce-mptcp-lte-wifi --disLte=true --iperfyC=true"
#   check results using the `for` loop
rm -rf files-*; rm *.pcap
NS_LOG="DceManager=level_function|func|prefix_time" ./waf --run "dce-mptcp-lte-wifi --disWifi=true"
```

The bandwidth of MPTCP should be close to the sum of WiFi-only and LTE-only.
