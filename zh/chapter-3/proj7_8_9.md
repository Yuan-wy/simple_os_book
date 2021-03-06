# proj7/8/9/9.1/9.2概述

为了实现虚存管理，首先需要能够处理缺页异常，这是需要对当前的trap处理进行扩展，并能够描述当前内核中“合法”的虚拟内存（不一定有对应的物理内存）。proj7在proj6的基础上实现了上述过程，新增加的主要工作包括：

- 描述当前“合法”的虚拟内存的数据结构vma\_struct和针对vma\_struct的函数操作；
- 扩展trap\_dispatch函数，使得能够根据vma\_struct结构的描述，正确完成对缺页的处理（即如果发现是“合法”的虚拟内存地址，则创建或修改页表项来建立与物理内存页的对应关系）。

为了提供超过物理内存大小的虚拟内存空间，需要把不常用的页换出到硬盘上，这样当访问到这些不存在的虚存页时，会产生缺页异常，可以把这些页再从硬盘拷贝回到内存中。proj8在proj7的基础上完成上述过程的实现，新增加的主要工作包括：

- 为了准备swap in/out，实现通过PIO方式读写IDE格式的硬盘；
- 建立swap相关数据结构和相关操作，确保不常用的页能够被换出（swap out）到硬盘上，并在被访问时，能够从硬盘对应的扇区中换入（swap in）到内存中;

为了实现将来不同进程（用户态程序）之间共享内存，需要对描述虚拟内存的vma_strct结构进行扩展。proj9/9.1在proj8的基础上完成上述过程的实现，新增加的主要工作包括：

- 增加shmem\_node结构的描述，确保能够描述多个虚拟页映射到一个物理页的情况，并增加针对shmem\_node的处理。

- 为了减少复制内存的开销，可通过实现写时复制（Copy On Write，简称COW）机制来完成，其基本思路是在只读情况下，多个虚拟页只需映射到一个物理页上，当对虚拟页进行写操作时，才真正完成对物理页的复制。在实现上需要对page的属性进行扩展，能够在发生页保护异常时，探测出是为了“写时复制”而设置的页，这样在缺页异常处理中，会完成实际的分配新页操作。proj9.2在proj9.1的基础上完成上述过程的实现，新增加的主要工作包括：

- 扩展trap\_dispatch函数，使得能够根据产生异常的地址的页表项内容和此地址对应的vma中的属性描述，正确完成对的“写时复制”处理。