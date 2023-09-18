# bpftime: Userspace unprivileged eBPF runtime for fast Uprobe & Syscall Tracing

`bpftime` is an unprivileged eBPF runtime designed to operate in userspace, offering rapid Uprobe and Syscall tracing capabilities.

## Features

- **Unprivileged Userspace eBPF**: Seamlessly run eBPF programs in userspace, attaching them to Uprobes and Syscall tracepoints just like in the kernel.
- **Performance**: Achieve up to 10x speedup compared to kernel uprobe and uretprobe.
- **Interprocess eBPF Maps**: Utilize userspace eBPF maps in shared userspace memory for summary aggregation or control plane communication.
- **Compatibility**: Use existing eBPF toolchains like clang and libbpf without any modifications. Compatible with kernel eBPF implementations without requiring privileged access.
- **Advanced Tooling**: Comes with a cross-platform eBPF interpreter and a high-speed JIT compiler powered by LLVM.

> ⚠️ **Note**: This project is actively under development. The API or design might change in upcoming releases, and it's not production ready yet.

## Quick Start

With `bpftime`, you can build eBPF applications using familiar tools like clang and libbpf, and execute them in userspace. For instance, the `malloc` eBPF program traces malloc calls using uprobe and aggregates the counts using a hash map.

To get started:

1. Navigate to the example directory:

   ```bash
   cd example/malloc
   ```

2. Build the eBPF program:

   ```bash
   make
   ```

3. Execute the libbpf tracing program:

   ```bash
   LD_PRELOAD=build/runtime/syscall-server/libbpftime-syscall-server.so ./malloc
   ```

4. Run the target program with eBPF:

   ```bash
   LD_PRELOAD=build/runtime/agent/libbpftime-agent.so ./test
   ```

Alternatively, you can run the program directly in the kernel:

```console
$ sudo example/malloc/malloc
15:38:05
        pid=30415       malloc calls: 1079
        pid=30393       malloc calls: 203
        pid=29882       malloc calls: 1076
        pid=34809       malloc calls: 8
```

## In-Depth

### **How it Works**

see [documents/how-it-works.md](documents/how-it-works.md) for details.

### **Examples & Use Cases**

We can use the bpftime userspace runtime:

- attach uprobe, uretprobe or all syscall tracepoints(x86 only) eBPF programs to a process or a group of processes
  - `malloc`: count the malloc calls in libc by pid
  - `bash_readline`: trace readline calls in bash
  - `sslsniff`: trace SSL/TLS raw text in openssl
  - `opensnoop`: trace file open or close in a process
- dynamically patch a running process
  - `hot_patch`: demostate how to patch real world CVE in redis or vim.

examples can be found in [example](example) dir.

### **Performance Benchmarks**

Userspace uprobe and tracepoint compared to kernel:

Hash map or ring buffer compared to kernel:

See [benchmark](benchmark) for detail performance benchmarks.

### Differences from Kernel eBPF and building eBPF programs

- some kernel helpers may not be avaliable in userspace
- Cannot directly access kernel data structures or functions like `task_struct`

## Build and test

see [documents/build-and-test.md](documents/build-and-test.md) for details.

## License

This project is licensed under the MIT License.