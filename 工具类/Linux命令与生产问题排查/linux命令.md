## 常见命令

### 内存

#### free

Linux free命令用于显示内存状态

语法

```
free [-bkmotV][-s <间隔秒数>]
```

```
# free //显示内存使用信息
total used free shared buffers cached
Mem: 254772 184568 70204 0 5692 89892
-/+ buffers/cache: 88984 165788
Swap: 524280 65116 459164
```

#### top

Linux top命令用于实时显示 process 进程 的动态。

top命令可以看到总体的系统运行状态和cpu的使用率 

语法

```
top [-] [d delay] [q] [c] [S] [s] [i] [n] [b]
```

**参数说明**：

- d : 改变显示的更新速度，或是在交谈式指令列( interactive command)按 s
- q : 没有任何延迟的显示速度，如果使用者是有 superuser 的权限，则 top 将会以最高的优先序执行
- c : 切换显示模式，共有两种模式，一是只显示执行档的名称，另一种是显示完整的路径与名称
- S : 累积模式，会将己完成或消失的子进程 ( dead child process ) 的 CPU time 累积起来
- s : 安全模式，将交谈式指令取消, 避免潜在的危机
- i : 不显示任何闲置 (idle) 或无用 (zombie) 的进程
- n : 更新的次数，完成后将会退出 top
- b : 批次档模式，搭配 "n" 参数一起使用，可以用来将 top 的结果输出到档案内

图片如下

<img src="..\..\Java学习\images\QQ截图20230604005624.png" style="zoom: 67%;" />

#### ps

用于显示当前进程的状态，类似于 windows 的任务管理器。

- **查看进程按内存从大到小排序**

   ps –e –o “%C:%p:%z:%a”|sort –k5 –nr

- 查看所有进程

  ps -ef

#### df

显示目前在 Linux 系统上的文件系统磁盘使用情况统计

- 显示所有的信息:

  ```
  # df --total 
  Filesystem     1K-blocks    Used    Available Use% Mounted on 
  /dev/sda6       29640780 4320720    23814372  16%     / 
  udev             1536756       4    1536752   1%      /dev 
  tmpfs             617620     892    616728    1%      /run 
  none                5120       0    5120      0%      /run/lock 
  none             1544044     156    1543888   1%      /run/shm 
  total           33344320 4321772    27516860  14% 
  ```

