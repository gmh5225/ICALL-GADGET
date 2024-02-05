# ICALL GADGET ABUSE 
### so i was scrolling around in IDA and found this 
![Gadget](https://cdn.discordapp.com/attachments/1130603399293976797/1204191936303603712/dataptrgadget.png?ex=65d3d622&is=65c16122&hm=5dc4028c16551634d26095909cd045eefbc9a49250cb113b290fe84e62dc28c4&)

# Original ASM
```
  sub     rsp, 38h
  mov     rax, cs:qword_67440
  test    rax, rax
  jz      short loc_D72E
  mov     r10, [rsp+38h+arg_20]
  mov     [rsp+38h+var_18], r10
  call    cs:__guard_dispatch_icall_fptr
  jmp     short loc_D733
  loc_D72E:                              
  mov     eax, 0C000001Ch
  loc_D733: 
  add     rsp, 38h
  retn
```
# Our Shellcode
### we are basically going to copy our shellcode that sets rax to a ptr to our handler function
```
 sub    rsp,0x38
 movabs rax,0xdeadbeef #placeholder for handler
 movabs r10,0xab39cfee
 inc    rax
 dec    rax
 call   QWORD PTR [rip+0x720d4]        # 0x720f8
 jmp    0x29
```

# Our code

```
  //getting address to function
	FunctionAddress = module + 0xD70C; 
  
	BYTE shellcode[] = { 0x48, 0x83, 0xEC, 0x38, 0x48, 0xB8, 0xEF, 0xBE, 0xAD, 0xDE, 0x00, 0x00, 0x00, 0x00, 0x49, 0xBA, 0xEE, 0xCF, 0x39, 0xAB, 0x00, 0x00, 0x00, 0x00, 0x48, 0xFF, 0xC0, 0x48, 0xFF, 0xC8, 0xFF, 0x15, 0xD4, 0x20, 0x07, 0x00, 0xE9, 0x00, 0x00, 0x00, 0x00 };

	memcpy(&shellcode[6], &hkfunction, 8);

	DisableWriteProtection();
	memcpy((PVOID)FunctionAddress, &shellcode, sizeof(shellcode));
	EnableWriteProtection();

```
