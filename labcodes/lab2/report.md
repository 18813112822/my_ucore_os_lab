# ��ϰ0
---
��ǰ��û�ù����diff��patch�Ĺ��ߣ�����ֱ��ʹ��svn������git����ؿͻ��˹��ߣ�����ʹ����һ�£�����������Ҳ�Ǻܲ���ģ�������Ϊ��֮ǰ���˽⣬����ֻ��ʹ����һ�»����Ĺ������������Ǹо��ظ������������˱ȽϵĶ�ģ�
�����ٲ鿴�ø���ݵĲ������߽ű��������������ڴ󲿷����ֹ�����ģ����Ժ����кõķ������ٽ��е�����

��ô����˵һ���ҵ�������

���ȣ�����ֱ�ӱȽ���kern�ļ��������в�ͬ���ļ�����Ϊ��lab1������Ҫ�޸ĵľ���kern�µĴ��룻��Ϊ�ļ����»������ļ��У���������Ҫ���õݹ�ķ�ʽ�����е��ã�������Ҫ����-r --brief�������������£�
```
prompt# diff -r lab2/kern/ lab1/kern/ > changelist
```
�����ͻὫkern�ļ����µ����в�ͬ���ļ���������������ǽ���Щ��ͬ���ļ���������һ��������������Ƕ����õ�changelist�У�����Ĳ������
```
Files lab1/kern/debug/kdebug.c and lab2/kern/debug/kdebug.c differ
Only in lab2/kern/debug: kdebug.c.orig
Only in lab2/kern/debug: kdebug.c.rej
Files lab1/kern/debug/kdebug.h and lab2/kern/debug/kdebug.h differ
Files lab1/kern/debug/kmonitor.c and lab2/kern/debug/kmonitor.c differ
Files lab1/kern/debug/kmonitor.h and lab2/kern/debug/kmonitor.h differ
Files lab1/kern/driver/console.c and lab2/kern/driver/console.c differ
Only in lab2/kern/init: entry.S
Files lab1/kern/init/init.c and lab2/kern/init/init.c differ
Files lab1/kern/libs/readline.c and lab2/kern/libs/readline.c differ
Only in lab2/kern/mm: default_pmm.c
Only in lab2/kern/mm: default_pmm.h
Files lab1/kern/mm/memlayout.h and lab2/kern/mm/memlayout.h differ
Files lab1/kern/mm/mmu.h and lab2/kern/mm/mmu.h differ
Files lab1/kern/mm/pmm.c and lab2/kern/mm/pmm.c differ
Files lab1/kern/mm/pmm.h and lab2/kern/mm/pmm.h differ
Only in lab2/kern/: sync
Files lab1/kern/trap/trap.c and lab2/kern/trap/trap.c differ
Files lab1/kern/trap/trap.h and lab2/kern/trap/trap.h differ
```
�������ǾͿ�����ͨ���������ı�������Щ�ļ��ǲ�ͬ�ģ�������������Լ���������������Ҫ��lab1������ӵĴ�����뵽lab2�У��������Щֻ����lab2�еĲ�ͬ���ǾͲ��ù����ˣ���������ı���ʾ�����ǿ��Է���kdebug.c����ļ������ǲ�ͬ�ģ���ô���ǿ���diffһ���������ļ�����������ʲô��ͬ��
```
6,9d5
< #include <sync.h>
< #include <sync.h>
< #include <kmonitor.h>
< #include <assert.h>
11,12d6
< #include <kmonitor.h>
< #include <assert.h>
310a305,341
> 	//print the important info of register ebp and esp
> 	uint32_t ebp_value;	//the value is the address of ebp register
> 	uint32_t eip_value;
> 	uint32_t argu_count = 4;
> 
> 	ebp_value = read_ebp();
> 	eip_value = read_eip(); 
> 	
> 	uint32_t i, j;
> 
> 	for (i = 0; i < STACKFRAME_DEPTH; i++)
> 	{
> 		cprintf("ebp:0x%08x eip:0x%08x ", ebp_value, eip_value);
> 		//using the uint32_t in order to simulated the addres of 32-bit
> 		//after the ebp register is [return address], and then calling function's arguments
> 		cprintf("args: ");
> 		uint32_t *argu_addr = (uint32_t*)ebp_value + 2;
> 		for (j = 0; j < argu_count; j++)	
> 		{
> 			cprintf("0x%08x ", argu_addr[j]);	
> 		}
> 		cprintf("\n");
> 		print_debuginfo(eip_value - 1);
> 		
> 		//to resolve the [error: invalid type argument of unary ��*��] problem: convert the type of eip_value/ebp_value to [uint32_t*]
> 		eip_value = *((uint32_t*)ebp_value + 1);//when the eip points to the return address, [pop arguments] will exec;
> 		ebp_value = *(uint32_t*)ebp_value; 
> 		//eip_value = ((uint32_t*)ebp_value)[1];		//because the value of ebp_value is address, so we can use uin32_t* to convert
> 		//ebp_value = ((uint32_t*)ebp_value)[0];
> 
> 
> 		cprintf("ebp:0x%08x ", ebp_value);
> 		cprintf("eip:0x%08x \n", eip_value);
> 	}
> 
> 
> 	return;

```
������Ҫ����һ����ǣ�������ʹ��diff�����ʱ���������ͨ��patch����a���b����ô��diff��ʱ���ʹ��`diff a b > delta`������delta�оͻش洢�����ļ��Ĳ�ͬ��֮����ʹ��`patch a c`ʱ�Ϳ�����aͨ������c���b���˽������diff��patch�в���λ�ô����ľ���Ч�������ǾͿ���ͨ�����ַ��������lab1�к��еĵ���lab2�в����еĴ����ˣ�

��Ϊ��������lab2�к���lab1ӵ�еĴ��룬��ô��ʵ�Ϳ��������������ò��ִ�����lab1�е����ӣ���Ϊ���Ǿ���Ҫ�������н�����`diff a b`��`a`��λ���滻��`lab2/kern/debug/kdebug.c`����`b`�е�λ���滻��`lab1/kern/debug/kdebug.c`��������ͬ�����delta�ļ��У�delta�ļ��д�ŵľ�����һ�����������ʾ�����ݣ�

��ϸ�۲����ǿ��Է��֣�lab1�µ�����ļ���������ȫ��lan2�µ�����ļ���ģ�kdebug-lab2�а����˸����ͷ�ļ���Ϣ���������ǲ����ܽ����delta�����ظ��������ļ�����Ϊ�����Ļ�patch��֮�����һ��������kdebug-lab1�ˣ���lab2�����ĺܶ�����û���ˣ�������patchû�����壻��Ϊ���ǽ�delta�ļ��У�kdebug-lab2����ӵ�е��ǲ���ȥ���������ĵ�`6,9d5`��`11,12d6`�����Ӧ�Ĵ���ȥ��������ʣ�µľ���kdebug-lab1�б�kdebug-lab2����ȫ�����Ķ��������ʱ��ִ���������
```
prompt# patch lab2/kern/debug/kdebug.c delta
```
�Ϳ��Խ�kdebug-lab1�������Ĵ��붼patch��kdebug-lab2�ϣ�
ʣ�µ������ļ����������ַ������о�������΢�е㱿׾����