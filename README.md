# i am writing this atm so come back later

# badeye

<img src="https://imgur.com/5MjFoHg.png"/>


# lsass.exe/csrss.exe

This section will go into detail about what exactly is going on here. csrss.exe/lsass.exe have handles to all processes and since battleye strips the R/W access of the handle that these processes have
to the game it can cause system instability. Thus bedaisy writes two pages of shellcode to both processes and inline hooks `NtReadVirtualMemory` and `NtWriteVirtualMemory`.

If you run a battleye protected game, open cheat engine, attach to `lsass.exe`, and navigate to `NtReadVirtualMemory`/`NtWriteVirtualMemory` you will see this inline hook...

<img src="https://imgur.com/E7KAeoV.png"/>

This inline hook jumps to shellcode that packages all of the parameter values passed to `NtReadVirtualMemory` into the stack and then jumps to `DeviceIoControl`...

<img src="https://imgur.com/DpFyC9p.png"/>

Now that you have a basic understanding of how this system works (and sorta why it is), lets look at what we can do!
To begin we need to extract the driver handle at runtime, this can be done simply by extracting the address of the shellcode out of the inline hook of `NtReadVirtualMemory`. Nnow that we have 
the handle to the driver we can start sending IOCTL's to BattlEye. The IOCTL data is not encrypted nor complicated... this is what it looks like:

<img src="https://imgur.com/fa627q3.png"/>

