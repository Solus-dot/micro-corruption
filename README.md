# Level 1 (New Orleans): 
(password: VD5'h=I) <br/> <br/>
Like in the tutorial, we will go through the `check_password` function and add a breakpoint to it. When we continue the program it asks us for the password. We put an random password and continue the program and as usual, it gives us the wrong password alert. When wew look at the `check_password` function, we come across an interesting line. 
```
44c2:  ee9d 0024      cmp.b @r13, 0x2400(r14)
```
We know that the address of our password is stored at `r13` but we can see that it is comparing it with `0x2400(r14)`. We know r14 goes from zero and increments by 1 for each character input. Let us check the stack address `0x2400` and sure enough, we get a string of characters that seems like the password. We end it by running the solve command and input the password to unlock the next level.
<br/>

# Level 2 (Sydney): 
(password: ig,b$Jtr) <br/> <br/>
The LockIT description says the password is not locally stored on the stack memory anymore. We check the `check_password` function again. 
`r15`contains the address of the password we entered,the instructions areo comparing every 2 byte stored in r15 with some value. These values make up the actual password. After doing a little research, I found out that the system is little-Endian. so that means for the first line we compare the first 2 bytes of `r15` with `0x69` and `0x67` (we reverse the bytes due to it being endian). Doing this for all the `cmp` instructions we build the password in hex:
```
6967 2c62 244a 7472
ASCII: ig,b$Jtr
```
We input this into the `solve` command and unlock the next level. 
<br/>

# Level 3 (Hanoi):
(password: 0000 0000 0000 0000 0000 0000 0000 0000 cf) <br/> <br/>
This time we do not have a `check_password` function. When we continue the program, we get a message:
```
Remember: passwords are between 8 and 16 characters.
```
This seemed interesting to me. We have a new `login` function to check now. We can see on the line `cmp.b #0xcf, &0x2410` that `&0x24100` has to be equal to `0xcf` which should be set by HSM. The password should have 16 bytes of random data and then `0xcf` for the 17th byte (This is an example of buffer overflow). Therfore, our password can be 16 bits of 0s followed `cf` as the 17th bit: 
```
0000 0000 0000 0000 0000 0000 0000 0000 cf
```
