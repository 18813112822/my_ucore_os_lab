# ��ϰ0
---
��ϰ0�Ĵ���ϳɷ������ǲ����������diff��patch�ķ������������������ǱȽϸ����ˣ�����Խ������Խ���ô���
һ��������п��ӻ������linuxϵͳ�Ļ������Ƽ�ʹ��meld��Ч��Ҫ��һЩ�����Ƕ����޷�ʹ�õĿ��ӻ��Ĺ��ߵ���������ǿ���ʹ��һ�´��ӵ�`diff`��`patch`����Ӧ��һ�¡�

---
# challenge 

��ս�⻹���ȷ����ڴˣ���ȫ����������ɺ���ȫ��һһ���ƣ�

---
# ˵��

��˵����Ҫ����������ʵ��1��ʵ��2֮����ֵ�����˵��������صĽ���취��

�����г��������ʵ��1��ʵ��2֮������`make qemu`ģ�����ϵͳ��������ʱ�����������⣺

```
ide 0:      10000(sectors), 'QEMU HARDDISK'.
ide 1:     262144(sectors), 'QEMU HARDDISK'.
SWAP: manager = fifo swap manager
BEGIN check_swap: count 31994, total 31994
setup Page Table for vaddr 0X1000, so alloc a page
setup Page Table vaddr 0~4MB OVER!
set up init env for check_swap begin!
page fault at 0x00001000: K/W [no page found].
page fault at 0x00002000: K/W [no page found].
page fault at 0x00003000: K/W [no page found].
page fault at 0x00004000: K/W [no page found].
set up init env for check_swap over!
write Virt Page c in fifo_check_swap
write Virt Page a in fifo_check_swap
write Virt Page d in fifo_check_swap
write Virt Page b in fifo_check_swap
write Virt Page e in fifo_check_swap
page fault at 0x00005000: K/W [no page found].
swap_out: i 0, store page in vaddr 0x1000 to disk swap entry 2
write Virt Page b in fifo_check_swap
write Virt Page a in fifo_check_swap
page fault at 0x00001000: K/W [no page found].
swap_out: i 0, store page in vaddr 0x2000 to disk swap entry 3
swap_in: load disk swap entry 2 with swap_page in vadr 0x1000
write Virt Page b in fifo_check_swap
page fault at 0x00002000: K/W [no page found].
swap_out: i 0, store page in vaddr 0x3000 to disk swap entry 4
swap_in: load disk swap entry 3 with swap_page in vadr 0x2000
write Virt Page c in fifo_check_swap
page fault at 0x00003000: K/W [no page found].
swap_out: i 0, store page in vaddr 0x4000 to disk swap entry 5
swap_in: load disk swap entry 4 with swap_page in vadr 0x3000
write Virt Page d in fifo_check_swap
page fault at 0x00004000: K/W [no page found].
swap_out: i 0, store page in vaddr 0x5000 to disk swap entry 6
swap_in: load disk swap entry 5 with swap_page in vadr 0x4000
count is 7, total is 7
check_swap() succeeded!
++ setup timer interrupts
0: @ring 0
0:  cs = 8
0:  ds = 10
0:  es = 10
0:  ss = 10
+++ switch to  user  mode +++
trap in T_SWITCH_TOU.the value of tf_eip is c010020c
page fault at 0xc010020c: U/R [protection fault].
not valid addr c010020c, and  can not find it in vma
trapframe at 0xc011ffb4
  edi  0x00000001
  esi  0x00000000
  ebp  0xc011ffa8
  oesp 0xc011ffd4
  ebx  0x00010094
  edx  0xc0108de7
  ecx  0x00000000
  eax  0x0000001e
  ds   0x----0023
  es   0x----0023
  fs   0x----0023
  gs   0x----0023
  trap 0x0000000e Page Fault
  err  0x00000005
  eip  0xc010020c
  cs   0x----001b
  flag 0x00003286 PF,SF,IF,IOPL=3
  esp  0xc011ffa0
  ss   0x----0023
kernel panic at kern/trap/trap.c:197:
    handle pgfault failed. invalid parameter

Welcome to the kernel debug monitor!!
Type 'help' for a list of commands.
```

������ʵ���������ǿ��Կ�������`check_swap`�����Ѿ��ɹ��ˣ�����������ϵͳ���ǲ�����`panic`��������Ϊʲô�أ�
�����ǵĳ��������Ԥ��Ĳ�һ��ʱ����ʼ�ĵ��Լ�������Ϊ��Ҫ����**��־��Ϣ**��**�򵥵Ĵ����߼�����**����Ϊ�������ų�bug�Ĺ�����Ҫ��ǳ������п���һ��bug����ͨ���򵥵Ĵ����߼��ƶϻ�����ϵͳ��־����ȫ���Թ۲���������ÿ�ζ���GDB�ȵ��Թ��߽��е��ԵĻ������鷳��˵�����Һ͸�bug��ص��߼��ܶ�Ļ���Ҫ�����￪ʼ��ϵ�۲�����أ��������ǵ��Եķ�ʽһ��Ҫ��ǳ�����Ҫ������ʹ�ø��ֵ��Թ���(��Ȼ��core dump�Ļ�����˵��)

�ص����������������У��������ǿ��Կ�������`switch to user mode`�Ĺ����в�����panic���������`switch to user mode`����������lab1����д��`lab1_switch_test`�����Ĳ��ֹ��ܣ���ô�������ڿ��Զ���������ط����ˣ�

�������ȿ�����`not valid addr c010020c, and  can not find it in vma`���⺯���������̷��ʵ������ַ�ռ������⣻����֪������һ���û�̬�Ľ�����˵��ÿ�����̶������Լ��������ַ�ռ䡣�����ַ�ռ��ڲ���ϵͳ����ʹ��һ��`mm_struct`�Ľṹ����й���ģ�����`mm_struct`�ṹ���ϻ�������������ַ����Ҫʹ�õ�`vma_struct`�������û�̬����ִ��ʱ��Ҫ�������������ַ�ռ�飻��������������ṹ��һ���û�̬��������������ķǷ������ַ�Ļ����ͻ�����û���Ǹ���Ӧ��`vma_struct`�����������������е�`not valid addr c010020c, and  can not find it in vma`.

��ôΪʲô`switch to user mode`֮��ͻ������������أ�������Ϊ������д`lab1_switch_test`��ʱ����Ϊ�����trackframe��ǿ�иı�������ջ���ݣ������Ļ�trapframe�д洢��EIP�Ĵ�����ֵ��ʵ�����ں�ʱ��EIPֵ(��Ϊtrap�������ں�̬�У����Բ�����ջ�л�)���������ʱ������CS��EIP���Ѿ���trapframe�е����ˣ���ôCS:IP�������ڵ��߼��ռ䣻����������������Ȼ��ģ�������ַ�ռ䣬EIP�е�ֵһ������`mm_struct`�н�������������`not valid addr c010020c, and  can not find it in vma`;

���涼���ҵ��ƶϣ�Ȼ����������ucoreԴ�����д�ӡ��һ��EIP��ֵ���������`the value of tf_eip is c010020c`����ʾ�ģ���Ȼ������ʵ�`valid addr`��ͬ������ƶϺ���