# no-ping
This is a simple XDP program written in P4, compiled using p4c-xdp extension.
This program drops all incoming ICMP packets.

## How to use?
Reference: https://github.com/vmware/p4c-xdp 

> Note: You need to always include `ebpf_xdp.h` with your program.

### Step 1: Generating C code from .p4 file
`p4c-xdp no_ping.p4 -o no_ping.c`

### Step 2: Generate the XDP program
`clang -Wno-unused-value -Wno-pointer-sign -Wno-compare-distinct-pointer-types -Wno-gnu-variable-sized-type-not-at-end -Wno-tautological-compare  -O2 -emit-llvm -g -c no_ping.c -o -| llc -march=bpf -filetype=obj -o no_ping.o`

> Note: You may face missing imports for `ebpf_kernel.h`, `ebpf_common.h`. As a quick fix, look for them under `~/p4c/backends/ebpf/runtime/` and make a copy to your local directory.

### Step 3: Load the XDP program
`sudo ip link set dev $INTERFACE xdp obj no_ping.o verb`

### Step 4: Remove the XDP program
`sudo ip link set dev $INTERFACE xdp off`