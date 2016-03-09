## 小组思考题
---

（2）(spoc) 有一台假想的计算机，页大小（page size）为32 Bytes，支持32KB的虚拟地址空间（virtual address space）,有4KB的物理内存空间（physical memory），采用二级页表，一个页目录项（page directory entry ，PDE）大小为1 Byte,一个页表项（page-table entries
PTEs）大小为1 Byte，1个页目录表大小为32 Bytes，1个页表大小为32 Bytes。页目录基址寄存器（page directory base register，PDBR）保存了页目录表的物理地址（按页对齐）。

PTE格式（8 bit） :
```
  VALID | PFN6 ... PFN0
```
PDE格式（8 bit） :
```
  VALID | PT6 ... PT0
```
其
```
VALID==1表示，表示映射存在；VALID==0表示，表示映射不存在。
PFN6..0:页帧号
PT6..0:页表的物理基址>>5
```
在[物理内存模拟数据文件](./03-2-spoc-testdata.md)中，给出了4KB物理内存空间的值，请回答下列虚地址是否有合法对应的物理内存，请给出对应的pde index, pde contents, pte index, pte contents。
```
1) Virtual Address 6c74
   Virtual Address 6b22
2) Virtual Address 03df
   Virtual Address 69dc
3) Virtual Address 317a
   Virtual Address 4546
4) Virtual Address 2c03
   Virtual Address 7fd7
5) Virtual Address 390e
   Virtual Address 748b
```

比如答案可以如下表示：
```
Virtual Address 7570:
  --> pde index:0x1d  pde contents:(valid 1, pfn 0x33)
    --> pte index:0xb  pte contents:(valid 0, pfn 0x7f)
      --> Fault (page table entry not valid)
      
Virtual Address 21e1:
  --> pde index:0x8  pde contents:(valid 0, pfn 0x7f)
      --> Fault (page directory entry not valid)

Virtual Address 7268:
  --> pde index:0x1c  pde contents:(valid 1, pfn 0x5e)
    --> pte index:0x13  pte contents:(valid 1, pfn 0x65)
      --> Translates to Physical Address 0xca8 --> Value: 16
```

> 计算答案

```
Virtual Address 0x6c74:
    --> pde index: 0x1b pde contens:(valid 1, pfn 0x20)
      --> pde index: 0x3 pde contens:(valid 1, pfn 0x61)
        Translates to Physical Address 0xc34 --> Value: 0x6

Virtual Address 0x6b22:
    --> pde index: 0x1a pde contens:(valid 1, pfn 0x52)
      --> pde index: 0x19 pde contens:(valid 1, pfn 0x47)
        Translates to Physical Address 0x8e2 --> Value: 0x1a

Virtual Address 0x3df:
    --> pde index: 0x0 pde contens:(valid 1, pfn 0x5a)
      --> pde index: 0x1e pde contens:(valid 1, pfn 0x5)
        Translates to Physical Address 0xbf --> Value: 0xf

Virtual Address 0x69dc:
    --> pde index: 0x1a pde contens:(valid 1, pfn 0x52)
      --> pde index: 0xe pde contens:(valid 0, pfn 0x7f)
        --> Fault (page directory entry not valid)

Virtual Address 0x317a:
    --> pde index: 0xc pde contens:(valid 1, pfn 0x18)
      --> pde index: 0xb pde contens:(valid 1, pfn 0x35)
        Translates to Physical Address 0x6ba --> Value: 0x1e

Virtual Address 0x4546:
    --> pde index: 0x11 pde contens:(valid 1, pfn 0x21)
      --> pde index: 0xa pde contens:(valid 0, pfn 0x7f)
        --> Fault (page directory entry not valid)

Virtual Address 0x2c03:
    --> pde index: 0xb pde contens:(valid 1, pfn 0x44)
      --> pde index: 0x0 pde contens:(valid 1, pfn 0x57)
        Translates to Physical Address 0xae3 --> Value: 0x16

Virtual Address 0x7fd7:
    --> pde index: 0x1f pde contens:(valid 1, pfn 0x12)
      --> pde index: 0x1e pde contens:(valid 0, pfn 0x7f)
        --> Fault (page directory entry not valid)

Virtual Address 0x390e:
    --> pde index: 0xe pde contens:(valid 0, pfn 0x7f)
      --> Fault (page directory entry not valid)

Virtual Address 0x748b:
    --> pde index: 0x1d pde contens:(valid 1, pfn 0x0)
      --> pde index: 0x4 pde contens:(valid 0, pfn 0x7f)
        --> Fault (page directory entry not valid)
```


