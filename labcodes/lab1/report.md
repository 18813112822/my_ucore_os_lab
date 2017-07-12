#��ϰһ

***

#ϰ��1

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

#��ϰ��
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


*�ޣ����������õĲ���֪ʶ*