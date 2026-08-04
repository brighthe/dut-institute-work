# SGSim 命令卡(22.04 WSL 速查)

> 完整手册见 [build-and-run.md](build-and-run.md)。本卡只记高频命令与红线,用于贴墙速查。

## 每次开终端

```bash
cd /home/brighthe/workspace/SGSim
source ~/sgsim-env.sh          # 提供 MPI 的 CPATH / LD_LIBRARY_PATH / shm
command -v mpicxx              # 必须 = /opt/intel/oneapi/mpi/2021.14/bin/mpicxx
```

## 日常编译与运行

```bash
cmake --build build/intelmpi-debug --parallel 16        # 改代码后
ldd build/intelmpi-debug/bin/SGFEM                       # 跑前查库:无 not found、MPI 是 Intel
cmake -E chdir build/intelmpi-debug/bin cmake -E env I_MPI_FABRICS=shm \
  ./SGFEM -i TestData/SGFem/Pre/StaticLin-LoadNested_fixed3.bdf \
  -w /home/brighthe/workspace/SGSim/workspace -j smoke
# 成功:退出码 0、后向误差 7.595360e-17、Job Finish
```

## 切求解器(CPardiso → PETSc/Hypre,套路相同)

```bash
source ~/sgsim-env.sh                                  # 必先,否则缺 mpi.h
sed -i 's/"CPardiso<Real_t>"/"TPetscKsp<Real_t>"/' Resource/cmake/SGConfig.cmake
#       Hypre: 把 TPetscKsp 换成 THypreKsp
cmake --preset linux_gcc_debug_intelmpi -B build/petsc-minimal
cmake --build build/petsc-minimal --parallel 16
cmake -E chdir build/petsc-minimal/bin cmake -E env \
  LD_LIBRARY_PATH=$PWD/build/petsc-minimal/lib:/opt/intel/oneapi/mpi/2021.14/lib:$PWD/Artifact/Ubuntu22-gcc11.4/lib \
  I_MPI_FABRICS=shm \
  /opt/intel/oneapi/mpi/2021.14/bin/mpiexec.hydra -n 1 \
  ./SGFEM -i TestData/SGFem/Task/Input/celas2.bdf -w workspace -j s1
# 成功:输出 Selected ... TPetscKsp / THypreKsp;KSP 收敛见 Iterations / CONVERGED
git checkout -- Resource/cmake/SGConfig.cmake            # 测完切回默认,勿 commit
```

## 同步三仓库(只在远程领先时)

```bash
git status --short --branch
git fetch origin
git merge --ff-only origin/dev                          # 有分叉就停下,不强推
# Artifact、ThirdParty 两仓:git -C Artifact/Ubuntu22-gcc11.4 ... 同套路
```

## 红线

1. **禁 `cmake --install`** — 会覆盖 Artifact 已跟踪预编译文件
2. **求解器只改 `SGConfig.cmake` 一个字符串**,已跟踪文件,测完 `git checkout --`,不 commit
3. **配置/编译前必须 `source ~/sgsim-env.sh`**
4. 同步只 `--ff-only`,不覆盖、不清理本地改动

## 参考数字

- 基线:主仓 `98038b6` / Artifact `08d3cfa` / ThirdParty `b0b6f19`(dev)
- 单测:468 项运行、464 过、4 skip、0 fail
- 最小算例后向误差 `7.595360e-17`,全流程 ~0.4 s
- 正式性能基线不在本机出(大小核混合)
