# NtUserInjectMouseInput-syscall
Not mine. Only for saving.

```C++
BYTE NtUserSendInput_Bytes[30];
BOOL MouseClass::LoadMouse()
{
	
	LoadLibrary(xorstr("user32.dll").c_str());
	HMODULE win32u = LoadLibrary(xorstr("win32u.dll").c_str());
	if (!win32u) return FALSE;
 
	// xor
	void* NtUserInjectMouseInput_Addr = (void*)GetProcAddress(win32u, xorstr("NtUserInjectMouseInput").c_str());
	if (!NtUserInjectMouseInput_Addr)	return FALSE;
 
	//*(void**)&NtUserInjectMouseInput = NtUserInjectMouseInputAddress; *For just using straight function*
	memcpy(NtUserSendInput_Bytes, NtUserInjectMouseInput_Addr, 30);
	return TRUE;
}
 
 
BOOLEAN WINAPI InjectMouseInputSpoof(mouse::InjectedInputMouseInfo* Struct, int Value)
{
	LPVOID NtUserInjectMouseInput_Spoof = VirtualAlloc(0, 0x1000, MEM_COMMIT, PAGE_EXECUTE_READWRITE); // allocate space for syscall
	if (!NtUserInjectMouseInput_Spoof) return FALSE;
	memcpy(NtUserInjectMouseInput_Spoof, NtUserSendInput_Bytes, 30); // copy syscall
	NTSTATUS Result = reinterpret_cast<NTSTATUS(NTAPI*)(mouse::InjectedInputMouseInfo*, int)>(NtUserInjectMouseInput_Spoof)(Struct, Value); // calling spoofed function
	SecureZeroMemory(NtUserInjectMouseInput_Spoof, 0x1000); // clean address
	VirtualFree(NtUserInjectMouseInput_Spoof, 0, MEM_RELEASE); // free it
	return (Result > 0); // return the status
}
```

https://www.unknowncheats.me/forum/anti-cheat-bypass/511819-move-mouse.html
