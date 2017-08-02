# ��ϰһ

***

# ϰ��1

��̫�����ˣ�д��һ���ֵĶ�����Ȼû�б��棬�ٴ򿪾�û���ˣ������Ժ�Ҫд��һ����ͽ����ǽ����ϴ���

����������һЩ��Ҫ��makefile�Ļ���֪ʶ��

* makefile���Խ�����C����������include�����ķ�ʽ�����£�

    ```
    include foo.make a.mk b.mk
    ```
    ����������a.mk��foo.mk���ǿ�������Ե�ǰMakefile����Ե�ַ�����Ǿ��Ե�ַ��
    
* Makfile��Ҳ�Ǻ���һϵ�е����ú�������addsuffix,basename,join�ȵȣ�

�����ڱ������ϵͳ�Ĺ�������Ҫ��Makefile��tools/function.mk�����ע���Ҷ�д���ˣ�����ֱ��ȥ�ۿ���



���������������������ļ��Ĺ��̣���ˮ�ˣ���������̿���ֱ��ͨ��make���۲죬����Ĺ�����:
```
make clean;make --just-print #��صĲ����������Ϥ�Ļ����Կ�������һ��дMakefile��
```

**ע**�����ǴӸ��ڵ㿪ʼ˵�ģ�������ִ��˳�򷴹����Ϳ����ˣ�

**�����Ǿ����ļ�**

1. ���ȴ������ļ���ʹ�õķ�����dd������ȴ���һ��[512*10000]���ļ������豸�ļ�/dev/zero���п�����Ĭ�ϵ�һ��blockΪ512Bytes��
2. ��������ļ��н�[��������]���ļ����ݿ�������һ��block�У�������512���ֽڣ�
3. Ȼ���ں��ļ��Ӿ����ļ��ĵڶ�������ʼ���п�����������Ҫʹ��seek��������һ��block��

(ע�������dd����copy & convert �ļ���������ص�conv=notruc��Щ�������Ծ���鿴man�ֲ�)

��ô������������ļ�����Ҫ[��������]��[�ں��ļ�]

**�������������**

�����Ǵ����������������س��򣩵Ĵ��룺
```
# create bootblock
bootfiles = $(call listf_cc,boot)
$(foreach f,$(bootfiles),$(call cc_compile,$(f),$(CC),$(CFLAGS) -Os -nostdinc))

bootblock = $(call totarget,bootblock)

$(bootblock): $(call toobj,$(bootfiles)) | $(call totarget,sign)
	@echo + ld $@
	$(V)$(LD) $(LDFLAGS) -N -e start -Ttext 0x7C00 $^ -o $(call toobj,bootblock)
	@$(OBJDUMP) -S $(call objfile,bootblock) > $(call asmfile,bootblock)
	@$(OBJDUMP) -t $(call objfile,bootblock) | $(SED) '1,/SYMBOL TABLE/d; s/ .* / /; /^$$/d' > $(call symfile,bootblock)
	@$(OBJCOPY) -S -O binary $(call objfile,bootblock) $(call outfile,bootblock)
	@$(call totarget,sign) $(call outfile,bootblock) $(bootblock)

$(call create_target,bootblock)
```

�����漰��̫���makefile���÷������˽�makefile���÷����޷��������������ﻹ��˳��ѧϰһ�±Ƚϸ��ӵ�makefile���÷��������Ժ��޸�makefile����ʹ��cmake������һЩ������������ƪ��������Ҫ��makefile֪ʶ�Һ��ڻ�ŵ��ҵ�[blog](https://markjenny.github.com)�ϣ�����Ȥ�Ŀ���ȥ����������д���ĵط���ӭ�����

�ܽ�һ�´������������Ĺ��̣�

���Ȼ�ȡ��boot�����е�Դ���ļ�����.c(c����)�ļ���.S��������ԣ��ļ�Ϊ׼
```
$(call listf_cc,boot)
```

���������Ƕ���һ�����Ҫ�γɵ�Ŀ������֣���Ϊbootblock��
```
bootblock = $(call totarget,bootblock)
```

����������һ����׼��makefile�д��ڵ�`target : prerequisites; command`����ʽ�ˣ��������£�
```
$(bootblock): $(call toobj,$(bootfiles)) | $(call totarget,sign)
	@echo + ld $@
	$(V)$(LD) $(LDFLAGS) -N -e start -Ttext 0x7C00 $^ -o $(call toobj,bootblock)
	@$(OBJDUMP) -S $(call objfile,bootblock) > $(call asmfile,bootblock)
	@$(OBJDUMP) -t $(call objfile,bootblock) | $(SED) '1,/SYMBOL TABLE/d; s/ .* / /; /^$$/d' > $(call symfile,bootblock)
	@$(OBJCOPY) -S -O binary $(call objfile,bootblock) $(call outfile,bootblock)
	@$(call totarget,sign) $(call outfile,bootblock) $(bootblock)
```

���������Ҫ�ǽ�bootasm.S��bootmain.c������ض����ļ��������ض����ļ�������������obj/bootblock.o��ִ���ļ���������`objdump -S -O `��������ʱû�ж������£�����bootblock.out�����ִ���ļ���������sign������д��������д������涨����������������ʽ��
1. �����������ĳ���Ϊ512���ֽڳ���
2. ��β�������Լ���0x55AA;

�������`dd if=bin/bootblock of=bin/ucore.img conv=notrunc`�������������Ĵ���д�뵽���̾����С�

**����ǲ���ϵͳ�ں�**

���ȣ���ȡ�������ں˴�����Ҫ��ͷ�ļ�Ŀ¼��Դ�ļ�Ŀ¼��������ʱ��Ҫ�ı������������������ʾ��

```
# kernel�а���ͷ�ļ�������includeĿ¼
KINCLUDE += kern/debug/ \
kern/driver/ \
kern/trap/ \
kern/mm/

# kernel�а���Դ�ļ�������sourceĿ¼
KSRCDIR += kern/init \
kern/libs \
kern/debug \
kern/driver \
kern/trap \
kern/mm

# ����kernel��Ҫ�ı���������
KCFLAGS += $(addprefix -I,$(KINCLUDE))
```

Ȼ�󣬽�ȡ������keinel�µ�Դ�ļ����б��룻
```
$(call add_files_cc,$(call listf_cc,$(KSRCDIR)),kernel,$(KCFLAGS))
```

Ȼ���ڶ�libs�µ�Դ�ļ����б��룬�������£�
```
$(call add_files_cc,$(call listf_cc,$(LIBDIR)),libs,)
```

��ȡlibsĿ¼�£�kernĿ¼�µ�Դ�ļ�������ȡ���ǵļ��ϣ�
```
KOBJS = $(call read_packet,kernel libs)
```

�����б������ӣ��������ɵĿ�ִ���ļ�����Ϊkernel��
```
$(kernel): $(KOBJS)
	@echo + ld $@
	# ִ�����ӽű�����kobjs����kernel����
	$(V)$(LD) $(LDFLAGS) -T tools/kernel.ld -o $@ $(KOBJS)
	@$(OBJDUMP) -S $@ > $(call asmfile,kernel)
	@$(OBJDUMP) -t $@ | $(SED) '1,/SYMBOL TABLE/d; s/ .* / /; /^$$/d' > $(call symfile,kernel)
```

���kernel�����ִ���ļ�д�뵽ucore.img����ĵڶ��������������£�
```
$(V)dd if=$(kernel) of=$@ seek=1 conv=notrunc
```

���Ͼ��Ǳ�������һ������ϵͳ���������̣�

*��ð������O(��_��)O�����ڼ�������*
```
#����bootblockʱ
objdump -S obj/bootblock.o > obj/bootblock.asm #Ӧ��ʱ�ÿ�ִ���ļ��Ļ�����
objdump -t obj/bootblock.o | sed '1,/SYMBOL TABLE/d; s/ .* / /; /^$/d' > obj/bootblock.sym #���ű�
objcopy -S -O binary obj/bootblock.o obj/bootblock.out
#����kernelʱ
objdump -S bin/kernel > obj/kernel.asm #Ӧ���Ǹò���ϵͳ����Ļ�����
objdump -t bin/kernel | sed '1,/SYMBOL TABLE/d; s/ .* / /; /^$/d' > obj/kernel.sym #���ű�

```

����Ĵ�����ʵ�봴��һ������ϵͳ�ľ����ļ��Ѿ�û��̫��Ĺ�ϵ�ˣ���ʱucore.img�Ѿ������ɹ��ˣ�����objdumpһ�������ڷ�������/����ʱ�����ʹ�ã��������������������ʹ�ã����ǻ�˵���Ѿ����������ӵ�һ�����¹�����ȴ�ˣ������ѧϰ����һ��~��

**jobjdump**�������ͨ�����־ͺ�������⣬��������չʾ��ִ���ļ������������Ϣ�ģ�����������Ҫ˵һ�����õ����������������壺
* -S ��Ҫ���ԡ�Դ���롱+�������롱�Ļ�Ϸ�ʽ����������˵���Ķ�Դ�����Ӧ��Щ�����룻
* -t ��Ҫ��չʾ���ÿ�ִ���ļ��ķ��ű�
������Щ����Ϊ�˺���ѧϰʱ�����õġ�

---

# ��ϰ��
***
Ϊ�˵���ucore�ĵ�һ��ָ���Ȼ�������Ƚӵ���������������Ҫ��֤��ִ���κ�һ��ָ����ֵ��۵�Ҫ��ֻ��ʹ��qemu��ģ���ˣ�ģ�������Ϊ,��
```
qemu -s -S -hda ./bin/ucore.img -monitor stdio
```
������������ʹ����monitor��Ϊ���ڵ��Թ�����(������`(qemu)`����������£���ʹ��monitor����ز���������)������

��Ϊ������Ҫ���Բ���ϵͳ����ʱִ�еĵ�һ��ָ����е�һ��ָ��ʱ���ڴ�Ϊʵģʽ8086�ܹ����ص㣩���������ʱ��ӱ��������޷�ʹ��gdb�ģ��������Ľ��̿ռ䶼��û�н�����gdbҲ��һ���û�̬���̣���Ȼ�޷�ʹ����~�����������Ǿ���Ҫʹ��Զ��gdb��
```
set architecture i8086
target remote 127.0.0.1:1234
file ./bin/kernel     #Ϊ����gdb�ڵ��ԵĹ����л�ȡ��������Ϣ
b *0x7c00            #�Ե�ַ���жϵ����õķ���
continue            #c ���Խ��м�дΪc
x /2i $pc #�鿴�������룬�����"2"��ʾһ����ʵ���з�������
stepi                #�Ի����������ʽ�������ԣ����Լ�дΪsi
```

ͨ������Ĵ�����Ϳ��Խ���һ����������ucore������һ������ָ��Ĺ��ܣ����������ͻȻð��һ���뷨Ҫ�˳�gdbģʽ��Ȼ���ٽ���gdb���е���ʱ����һ����Ҫ������ô����صĲ�������ľ�кܷ�������Ա��ʱ��ܱ���ĺò��û�������ʱ��ȥլ�أ�����һ��͵���ķ������ǽ���Щ����д��һ�����ƽű����ļ������ֹ�����gdb��ʱ���ڵģ���ֻҪ��`gdb -x gdbscripting`����ģʽ��gdbscripting�е�������ͻ���gdb��ģʽ������ִ�У���ʵ���ǽ�gdb����Ҫִ�е��������ִ�У�

> �ӱ�׼�𰸵ı�����ѧϰ�������qemu������ʹ�÷�ʽ��`-d: item1:enable logging of specified item`�����ض������Ϣ������־��ʽ�������Ĭ�������������qemu���������½���ֱ���������Щʱ�޷����沢������鿴�����ǿ���ʹ��`-D logfile`����logging����Ķ���ָ������logfile����־�ļ��У��Ա�۲�Աȣ�

�� *�ޣ����������õĲ���֪ʶ* ��

ͨ����gdb�в鿴Ҳ�۲�������������е�bootload�ڵ���bootmain֮ǰ�û�����(�ǵ�ҪҪ��gdb�����м���`x /10i $pc`������ǰ�û�������ǿ�Ʒ����Ŷ)��bootasm.S��bootblock.asm�еĴ�������ͬ�ģ�

�����һ��memset���ں˴��룬ֱ��ʹ��������Ϣ���öϵ㣺

```
break memset #��˵��һ�㶼ֱ��ʹ��b����ʾbreak�����Ƕ�����һ��������ˣ�д̫�ٵĴ���ò�Ʋ��ã�23333
```
`continue`֮��ͻ���memest����breakס��

---

# ��ϰ��

---

����intelоƬ�����¼��ݵ�ԭ���������⹹�����A20 gate��ͨ�����������Ƿ���С� *����* ������ʹ����80386���cpuʱ��������ʵģʽ���Ǳ���ģʽ�����Ƕ���Ҫ��A20ʹ�ܣ���֤���������ʹ�ôӶ����������ƣ�����bootloader���еĹ�����Ҫ�����¼�����Ҫ�Ĺ��̣�
* ʹ��A20;
* ��ʼ��ȫ������������֤����ȷ��������/�����������ݵ�λ�ã�
* ʹ��[����ģʽ]��
* ���ucore�Ĵ��룬���ݵȼ��ص��ڴ��У�

��ֻ��һ�����µĹ��̣�ʵ���ϸ���bootasm.S��������û�취����һЩcpuָ��߼�������û�а취�����������ģ�����ʹ�û�����ԣ��������кܶ��ϸ�ڵģ�

����������Ҫ����һЩ��ʼ���Ĺ���
```
cli #clear interrupt flag,�����X86�ܹ���Flag�Ĵ����ϵ�һ����־λ������clear�����ñ�ʶλ�����������ˣ�Ҳ�ͽ�ֹ�����ж���
cld #clear direction flag,���Ҳ��һ����־λ��������Ϊ�վ��Ǳ�֤�������������ÿ��ִ��֮�������ߵ�ַȥ����ִ�У����Բο�[������](https://stackoverflow.com/questions/9636691/whats-the-difference-between-cld-and-std-instructions-in-assembly-language)

# ������Ҫ�ǽ���Ҫ���漰����Ҫ�����ݶμĴ����ÿգ�������������ʹ�õ���movw��������mov������Ϊ���ڻ���ʵģʽ��wָ�����ǲ�������һ�����֡������Կ���cs��app������ˬ�ġ�������ԡ���
xorw %ax, %ax #���֤�üĴ�����ֵΪ��
movw %ax, %ds #�ÿ����������μĴ�����ֵ
movw %ax, %es
movw %ax, %ss
```

���������Ǿ���Ҫ��A20 Gate��������֤����ȡ����1MB���ڿؿռ��ǲ��ᷢ����صġ� *����* ���Ӷ������ڱ���ģʽ��ʹ������32bit��4GB�ռ䣬����˵���ǽ�8042��Ƭ����һ��������Ϊ�ߵ�ƽ��������Կ�gitbook��ʽ��ָ���飬�����ⲿ�ִ��벻�ٽ������ã�ֱ�ӿ�����ɣ�̫����û�������ı�Ҫ�ˣ�������ʦ�������ҵ�ʵ�鱨�棬��ֻҪ������Ҫ����׼ȷ���ɣ�����λ��boot/bootasm.S�е�29~43�У�

���������Ǿͳ�ʼ��GDT(global description table)������ȫ������������ص��ڴ��У�����ʼ��[GDTR](https://en.wikibooks.org/wiki/X86_Assembly/Global_Descriptor_Table)
```
lgst gdtdesc
```
��˵һ�䣬������ѧϰ���ۿε�ʱ��֪����GDTR�����ã����GDT���������ۿ��е�֪GDTR�в���������GDT�ĵ�ַ����������GDT�ĳ��ȣ����ǴӴ����ж����Կ�����
```
gdtdesc:
.word 0x17                                      # sizeof(gdt) - 1
.long gdt                                       # address gdt
```

��ʼ���μĴ�����A20��ȫ����������֮�����ǾͿ���ʹ�ܱ���ģʽ�ˣ�����漰��Ҫ�޸�CR0���ƼĴ����ĵ�0bit������bitλ��Ϊ1��ʹ����`����ģʽ`������Ļ�����������ʾ��
```
movl %cr0, %eax
orl $CR0_PE_ON, %eax
movl %eax, %cr0
```

�����е��ڴ�ģʽ���óɱ���ģʽ����Ҫ������CS�Ĵ�����EIP�Ĵ�����ֵ����bootssm.S��ʹ���˳���תָ�����趨CS�Ĵ�����EIP�Ĵ�����ֵ����Ŀǰ��Ϊ��תָ��`jmp`���ڶ��ڽ��н�����ת��`ljmp`���ڶμ������ת�����Կ�һ��[����](https://docs.oracle.com/cd/E19455-01/806-3773/instructionset-73/index.html);
```
ljmp $PROT_MODE_CSEG, $protcseg
```
ͨ��bootasm.S�����еı���`PROT_MODE_CSEG`Ϊ�ں˵Ĵ����ѡ���ӣ���CS�Ĵ�����ֵ��`protcseg`Ϊ��ִ�еĴ��룬���������Ѿ���CS��EIPȫ�����ú��ˣ�����������ִ�д���εĴ����ˣ�

��call bootmain֮ǰ����Ҫ�����ݶ���صļĴ������г�ʼ�����ã�����:
```
# Set up the protected-mode data segment registers
    movw $PROT_MODE_DSEG, %ax                       # Our data segment selector
    movw %ax, %ds                                   # -> DS: Data Segment
    movw %ax, %es                                   # -> ES: Extra Segment
    movw %ax, %fs                                   # -> FS
    movw %ax, %gs                                   # -> GS
    movw %ax, %ss                                   # -> SS: Stack Segment
```
��ʼ�������ݶ���ؼĴ����󣬳�ʼ����ջ��صļĴ�����
```
movl $0x0, %ebp
movl $start, %esp
```
ebp�Ĵ����Ǳ������µ�ջ����Ϣ����esp�������ĳһʱ�̵�ջ����Ϣ����Ϊ�˺������ÿ��Խ��е�����ָ���ʹ�õģ�

���`call bootmain`

---

# ��ϰ��

---
������Ҫ����һ��ʵ��ָ�����е���ظ�����
> bootloader����Ӳ�̵��Ƿ�ʽ��LBAģʽ��PIO��Program IO����ʽ��������Ӳ�̵ķ�ʽ��ͨ��CPU����Ӳ�̵�**IO��ַ�Ĵ���**����ɵģ�

��������д���IO��ַ�Ĵ���������ͨ����IO��ַ�Ĵ������и�ֵ���Ӷ����CPU��Ӳ�̵Ŀ��Ƽ����ݶ�ȡ��IO��ַ�Ĵ����ֱ�Ϊ0x1f0-0x1f7,0x170-0x17f,ͨ������ЩIO��ַ�Ĵ������趨�����Ա�֤CPU��Ӳ�̽�����صķ��ʣ�

���IO��ַ�Ĵ�����ֵ����ع������£�

|IO��ַ|����|
|---|:---|
|0x1f0|�����ݣ���0x1f7��Ϊæ״̬ʱ�����Զ���|
|0x1f2|Ҫ��д����������ÿ�ζ�дǰ������Ҫ������Ҫ��д������������С��1������|
|0x1f3|�����LBAģʽ������LBA������0-7λ|
|0x1f4|�����LBAģʽ������LBA������8-15λ|
|0x1f5|�����LBAģʽ������LBA������16-23λ|
|0x1f6|��0~3λ�������LBAģʽ����24-27λ ��4λ��Ϊ0���̣�Ϊ1����|
|0x1f7|״̬������Ĵ���������ʱ�ȸ�����ٶ�ȡ���������æ״̬�ʹ�0x1f0�˿ڶ�����|

ͨ��������ЩIO��ַ�Ĵ������Ӷ�����CPU����Ӳ�̵������Ϊ��

CPU�ڶ�ȡӲ�̵�����ʱҪ�ȵȴ�Ӳ�̿�����֮�������Ӳ�̷�����ȡ�������������Ҳ��Ҫ�ȵ�Ӳ�̿���ʱ�����ܽ�Ӳ�̵����ݶ�ȡ���ڴ������������ڶ�ȡӲ�����ݵĲ���Ϳ��Էֳ����²��裺

* �ȴ�Ӳ�̿��У�
* ������ȡӲ�̵����
* �ȴ�Ӳ�̿��У�
* ��Ӳ�����������ݶ�ȡ���ڴ��У�

�� *�����Ҿ�����Ի�·��������һֱ��Ϊʱ��kernel.ld�е�0x10000��bootmain.S�е�0x1000��һ��ֵ��ֻ�����ǿ���д���˵����⵼�¿�������һ��==������������Ϊ�Լ����Ի�·�ҽ�ڵ�ʱ�򣬷����ҵ��Ի�·�����⣬��ô�ͽ���ȷ��д�����ɣ������Ҿ��ÿ��Զ�0x1000���켸�䣬�����ҵ�ͬ��Ҳ�㲻��0x10000��0x1000��ʲô����23333����˵�������ϲ�����һ�£��������ǶԴ�����ͬ�Ľ��ͣ�����˵�����ǳ����㣿* ��

����bootmain�����У����Ƚ��е��ǽ������ļ��ĵڶ���������ʼ�����ݽ��ж�ȡ��

```
void
bootmain(void) {
    // read the 1st page off disk
    readseg((uintptr_t)ELFHDR, SECTSIZE * 8, 0);

    //...
}
```
��������е�readseg�����Ǵ�Ӳ���ж�ȡ`SECTSIZE * 8`���ȵ����ݷ��õ�`ELFHDR`��������ַ�У����ʱ���Ҿ��е�㲻�����`0x10000`�������ˣ��ҵ�Ǳ��ʶ��Ȼ������kernel.ld�е�`0x100000`��Ч��һ�������ˣ���ʵ�����ǲ�һ�������ǵ������ǲ�ͬ�ģ�Ϻ�����ҽ�Ϊ��ϸ�ز���һ��ΪʲôҪʹ��`0x10000`��`0x100000`��
����`0x1000`��һ���ݴ�ĵ�ַ�������ǽ���Ӳ���ж�ȡ���Ĵ��������ȷ���������ط���֮���ٽ���Щ���ݺͳ�����ص�������Ҫ���صĵط�(����֪������boot����ʼ�׶��л���������0x7c00����ʼִ�У���0x7c00����ȡ��������0x10000������0x10000-0x7c00֮����ȫ���Է�����һ�����������ݣ�������ҵĽ���23333)��

�����������ٽ���һ��`0x100000`������ݵ����ã����������kernel������س���ε������ַ�ĵط��� ע������ļ��ع��̣��Ҿ��á�����Ա�������������Ȿ�齲��ͦ��ϸ�ģ������ַ���Բο�һ���ҵ�[����](https://markjenny.github.io/difference_between_virtualaddress_logicaddress_and_linearaddres/) ��������ͨ����ȡkernel�ļ�Ҳ���Կ�������

```
Elf file type is EXEC (Executable file)
Entry point 0x100000
There are 3 program headers, starting at offset 52

Program Headers:
  Type           Offset   VirtAddr   PhysAddr   FileSiz MemSiz  Flg Align
  LOAD           0x001000 0x00100000 0x00100000 0x0d6a9 0x0d6a9 R E 0x1000
  LOAD           0x00f000 0x0010e000 0x0010e000 0x00a16 0x01d80 RW  0x1000
  GNU_STACK      0x000000 0x00000000 0x00000000 0x00000 0x00000 RWE 0x10

 Section to Segment mapping:
  Segment Sections...
   00     .text .rodata .stab .stabstr 
   01     .data .bss 
   02 
```
ͨ�����Ŀ��Կ���������Σ�FlgΪR E����Ҫ���ص������ַ`0x100000`�ϣ������ݶ�(FlgΪRW)�Ǽ��ص������ַ`0x0010e000`�ϣ���Щ��Ϣͨ��kernel.ld��Ҳ���������ؿ�������

���ǽ����������ߣ���ȡbootmain���ȶ�ȡ��һ��page�����ݣ��������Ƿ����������ж�ȡ�ģ���һ������һ�������ض�ȡ�����´�����ʾ��

```
static void
readseg(uintptr_t va, uint32_t count, uint32_t offset) {
    uintptr_t end_va = va + count;

    // round down to sector boundary
    va -= offset % SECTSIZE;

    // translate from bytes to sectors; kernel starts at sector 1
    uint32_t secno = (offset / SECTSIZE) + 1;

    // If this is too slow, we could read lots of sectors at a time.
    // We'd write more to memory than asked, but it doesn't matter --
    // we load in increasing order.
    for (; va < end_va; va += SECTSIZE, secno ++) {
        readsect((void *)va, secno);
    }
}
```

����Ĵ��붼�ȽϺ���⣬��һ������һ�������ض�ȡ����ȡ�ɹ�֮��͸��������ַ�Ա���һ������Ҫ���õ������ַ�ĵط�������һ��������

���������еĺ���`readsect`�����þ�������ǰ�潲�ģ�ͨ����IO��ַ�Ĵ�����ֵ������CPU��Ӳ�����ݵĶ�ȡ���Լ����Խ��жԣ��ҾͲ�׸���ˡ�

���ڴ���λ������ݶ���˵��ÿ���ζ�����һ���α�section header,������������λ������ݶη��õ������ַ��λ�ã��������ݵĳ��ȣ�ͨ��`readsect`��������Щ���ݴ�֮ǰ�ڴ���ݴ�λ�÷��õ���Ҫ���λ���ϣ��α�Ľṹ���Բ鿴elf.h�ļ������£�

```
/* program section header */
struct proghdr {
    uint32_t p_type;   // loadable code or data, dynamic linking info,etc.
    uint32_t p_offset; // file offset of segment
    uint32_t p_va;     // virtual address to map segment
    uint32_t p_pa;     // physical address, not used
    uint32_t p_filesz; // size of segment in file
    uint32_t p_memsz;  // size of segment in memory (bigger if contains bss��
    uint32_t p_flags;  // read/write/execute bits
    uint32_t p_align;  // required alignment, invariably hardware page size
};
```
���ͨ��kernel�����file header�е�`e_entry`ָʾ��������ڣ�������Ȩ������kernel���ؼ���������

```
    // call the entry point from the ELF header
    // note: does not return
    ((void (*)(void))(ELFHDR->e_entry & 0xFFFFFF))();
```

���ˣ�����kernel�ļ��ز�������Ȩת�ƵĹ��̽�����

----
# ��ϰ��
----

��ϰ����Ҫ����Ե���ջ������⼰��صļ�ʵ�������Ƕ�����������ջ�ĵ��û��Ǻ��а����ģ������һ��ǻ����ҵ�blog������һ���򵥵Ķ�ջ���õĽ��⣻

����һ��ʵ��𰸣����ִ𰸻�������ʼ��״̬��ebp�����˽��ͣ���ô��Ҳ�������˼·����һ�½��ͣ�����û�а���ʵ��𰸵�˼·���ߣ�����Ȥ�Ŀ��Կ�һ�£�

����ָ����ִ�б����ú�����ʱ����ִ�����µĻ��ָ�
```
push ebp
mov esp ebp
```
�����µ�ebp��ָ��Ķ�ջ�ڴ��б����ʱԭ����ebp��ֵ(���ڴ�λ����ջ���������Ǻ������ִ�У����ڴ�λ����ջ�׷������Ǹñ����ú�����return address������ʵ��ֵ��Ҳ���ܲ����в�����),��������ִ�����ʱ���ֻ�ִ��һ�����»��ָ�
```
pop ebp
```
�ֽ�ԭebp��ֵ�����˻ָ�������ͨ������ebp��ֵ���洢ԭ��ebp��ֵ���ͻὫ���õĺ����γ�һ��**���ö�ջ��**���Ӷ���ȷ��ִ�к������ã�

���ǿ���ͨ����ʵ���һ���������������һ�£�
```
moocos-> cat tmp
 make qemu
(THU.CST) os is loading ...

Special kernel symbols:
  entry  0x00100000 (phys)
  etext  0x001032f9 (phys)
  edata  0x0010ea16 (phys)
  end    0x0010fd20 (phys)
Kernel executable memory footprint: 64KB
ebp:0x00007b08 eip:0x001009ad args: 0x00010094 0x00000000 0x00007b38 0x00100092 
    kern/debug/kdebug.c:311: print_stackframe+28
ebp:0x00007b18 eip:0x00100ccb 
ebp:0x00007b18 eip:0x00100ccb args: 0x00000000 0x00000000 0x00000000 0x00007b88 
    kern/debug/kmonitor.c:125: mon_backtrace+10
ebp:0x00007b38 eip:0x00100092 
ebp:0x00007b38 eip:0x00100092 args: 0x00000000 0x00007b60 0xffff0000 0x00007b64 
    kern/init/init.c:48: grade_backtrace2+33
ebp:0x00007b58 eip:0x001000bb 
ebp:0x00007b58 eip:0x001000bb args: 0x00000000 0xffff0000 0x00007b84 0x00000029 
    kern/init/init.c:53: grade_backtrace1+38
ebp:0x00007b78 eip:0x001000d9 
ebp:0x00007b78 eip:0x001000d9 args: 0x00000000 0x00100000 0xffff0000 0x0000001d 
    kern/init/init.c:58: grade_backtrace0+23
ebp:0x00007b98 eip:0x001000fe 
ebp:0x00007b98 eip:0x001000fe args: 0x0010331c 0x00103300 0x0000130a 0x00000000 
    kern/init/init.c:63: grade_backtrace+34
ebp:0x00007bc8 eip:0x00100055 
ebp:0x00007bc8 eip:0x00100055 args: 0x00000000 0x00000000 0x00000000 0x00010094 
    kern/init/init.c:28: kern_init+84
ebp:0x00007bf8 eip:0x00007d68 
ebp:0x00007bf8 eip:0x00007d68 args: 0xc031fcfa 0xc08ed88e 0x64e4d08e 0xfa7502a8 
    <unknow>: -- 0x00007d67 --
ebp:0x00000000 eip:0x00007c4f 
ebp:0x00000000 eip:0x00007c4f args: 0xf000e2c3 0xf000ff53 0xf000ff53 0xf000ff53
```
���ǿ��Կ���ebp��ֵ�����±仯�ģ�
`0x00000000`��'0x00007bf8'��'0x00007bc8'��`0x00007b98`��`0x00007b98`��`0x00007b78`��`0x00007b58`��`0x00007b38`��`0x00007b18`��`0x00007b08`��

���������ı仯������Ҫ˵һ�£�

1. Ϊʲô��Ҫ������д�أ���Ϊ�Ҵӳ�ʼ����״̬��ʼ�ģ������ڲ���ϵͳ��bootloader���ʼ����صļĴ���/A20/ȫ������������ջ��Ϣ������ebpΪ0x00000000��espΪ0x00007c00�������Գ�ʼ״̬��ebp����0x00000000��
2. Ϊʲô֮��ebp��ֵ��һֱ�ڱ�С�ģ���Ϊ��ջ���������������ַ�ռ�С�ķ�����еģ�

---