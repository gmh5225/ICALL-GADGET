# ICALL GADGET ABUSE FOR COMMUNICATION
## so i was scrolling around in IDA and found this 
![Gadget](https://cdn.discordapp.com/attachments/1130603399293976797/1204191936303603712/dataptrgadget.png?ex=65d3d622&is=65c16122&hm=5dc4028c16551634d26095909cd045eefbc9a49250cb113b290fe84e62dc28c4&)
## after further inspection we can see that it calls a function called _guard_dispatch_icall_ptr
## the icall basically is a jmp to rax so if you do some thinking we can modify this entire function to instead call our handler

# Our Shellcode
## we are basically going to copy our shellcode that sets rax to a ptr to our handler function. We want to make sure that when we make our shellcode the exact same size of bytes as the code we are modifying
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
## in cpp we need to just get the address to the function then map our shellcode into it but there is a trick here if we do this simply how it is we will corrupt the stack and bluescreen from time to time. To avoid this we will need to repair the stack by insteaed of returning inside of handler performing the same operations the functions should be doing I will leave this portion to the user because i dont want to spoonfeed a amazing method of evading the anticheats but remember to look at the original asm of the function and see what we are modifying. SIDE NOTE : if you fix the stack corruption and instead of returning do what the function was inteaded to do in your handler it will appear as nothing has been modified :)
```
  //getting address to function
	FunctionAddress = module + 0xD70C; 
  
	BYTE shellcode[] = { 0x48, 0x83, 0xEC, 0x38, 0x48, 0xB8, 0xEF, 0xBE, 0xAD, 0xDE, 0x00, 0x00, 0x00, 0x00, 0x49, 0xBA, 0xEE, 0xCF, 0x39, 0xAB, 0x00, 0x00, 0x00, 0x00, 0x48, 0xFF, 0xC0, 0x48, 0xFF, 0xC8, 0xFF, 0x15, 0xD4, 0x20, 0x07, 0x00, 0xE9, 0x00, 0x00, 0x00, 0x00 };

	memcpy(&shellcode[6], &hkfunction, 8);

	DisableWriteProtection();
	memcpy((PVOID)FunctionAddress, &shellcode, sizeof(shellcode));
	EnableWriteProtection();

```

# Wrap Up
## hopefully you took something from this quick write up just know that you can find other gadgets aswell that will do tons more the more complex the better 
