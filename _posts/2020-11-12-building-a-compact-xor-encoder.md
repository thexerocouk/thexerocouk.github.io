---
layout: post
title:  Building A Compact XOR Encoder
date:	2020-11-12 
author: TheXero
comments: true
categories: blog
description: The majority of memory corruption exploits that exist, have some form of input character limitation. To get around these limitations, you have what is known as an encoder. By encoding the input ...
excerpt: The majority of memory corruption exploits that exist, have some form of input character limitation. To get around these limitations, you have what is known as an encoder. By encoding the input ...
tags: [exploit, encoder, shellcode]
thumbnail: /images/encoder.png
image: /images/encoder-200x200.png
---

The majority of memory corruption exploits that exist, have some form of input character limitation. To get around these limitations, you have what is known as an encoder. By encoding the input characters, it may be possible to avoid several of the so called 'bad characters' within generated shellcode.

The simplist form of an encoder uses the XOR method, so this is what we will be using to build our first encoder. In summary, if you ran the same XOR on the same data twice, you are back were you started. This means, your encoder will also act as the decoder, this is because of the way XOR works.

As part of our exploitation engine, we built a custom made shellcode encoder using this XOR method and the EAX register. 

Before we begin the development of the encoder, it helps to first plan out what the encoder should look like using plain pseudocode as shown below.

    clear the register
    save the current address
    get the length of the shellcode
    xor the current byte of shellcode 
    increment the address
    did we reached the end
    jump back to the xor

The above code, sets out a clear path in plain-english for what we want and how we want to achieve this. 

To turn the above pseudocode into the raw assembly code, it is easier and faster to write this directly into debugging software.

The first instruction, is to clear a register. For this, we can use XOR. If you XOR something by itself, you should be getting a zero output.

    XOR ECX,ECX;

The next step is to save the location of where we currently are within the stack, would require the following two steps.
The first, is we want to issue a CALL against itself plus six, this will push the current address to the top of the stack and move to the next instruction. The next thing we want to do, is store that value, so we can access it later, for this, we will POP this value into the EAX register. We will be referring to this register later on within the code.

    CALL FFFFFFF; 
    INC ECX;
    POP EAX;

For this next step, to save on space, we created two versions of the same code. One for small lengths of shellcode, and one that supports much larger shellcode. We want to store the length of our shellcode to another register, so we can refer to this later to know when we have reached the last address we need to decode.

For short shellcode, we want to store the length of the shellcode with the ECX register. However, the register itself is a full 32-bits in length, but we will only be needing 8 of those 32-bits of space. The effective register to store our shellcodes' length is the CL register.

    MOV CL,0FF;

For larger shellcode, we can use 16-bits of ECX, or the CX register.

    MOV CX,0FF;

As we stored the length of the shellcode into parts of the zeroed ECX register, we now want to make it a complete address. To do this, we take the current address location we stored in EAX, and add this to the value at ECX. By doing this, we now have the full address in memory for where our shellcode ends.

    ADD ECX,EAX;

Now comes the interesting part, the actual encoding portion of our code.

As none of the registers contain our shellcode, we want to perform an XOR on the memory that the register points to.

We also want to XOR it by a value of our choosing, the seed or key used to XOR the data. We can use the following assembly instruction.

    XOR BYTE PTR DS:[EAX+F],0F;

What this code would actually do, is XOR the byte stored at the memory address of EAX+16, with a seed or key of 0F. 

Next what we need to do is increment our EAX counter, so we can keep track of what bytes have been XORed already.

    INC EAX;

Once we have encoded a single line of shellcode, we want to check to see if there is more encoding to be done. For this, we will use the assembly instruction to compare the address of the beginning, with the total length of the shellcode.

    CMP EAX,ECX;

If the value of EAX matches ECX, then the compare statement returns true, otherwise, false is returned.

The final instruction forms part of a loop. If the value returned from the previous command is true, it indicates that the next instruction should not be executed. However, if the value was false, then the next instruction should be executed.

    JNZ SHORT F7;

This instruction, tells the program to return back to the XOR command, and continue with the process until the value returned at the previous compare statement returns a true value.


In this example, the values for the shellcode length and seed are static. However, when implemented into the nullsploit project, these values are generated at run time. This ensures that the length of the shellcode is accurate, and that the value of the seed is dynamic. 

The completed x86 encoder shellcode resembles the below.

    00401000 >   33C9           XOR ECX,ECX                              ; Zero ECX
    00401002     E8 FFFFFF      CALL 00401006                            ; Push EIP
    00401006     FFC1           INC ECX                                  ; Increase ECX
    00401008     58             POP EAX                                  ; Get EIP
    00401009     B1 FF          MOV CL,0FF                               ; Mov length of shellcode into CL
    0040100B     03C8           ADD ECX,EAX                              ; Add EIP to ECX
    0040100D     8070 0F 0F     XOR BYTE PTR DS:[EAX+F],0F               ; XOR byte at EAX+0F with seed
    00401011     40             INC EAX                                  ; Increase EAX
    00401012     3BC1           CMP EAX,ECX                              ; Compare ECX to EAX
    00401014    ^75 F7          JNZ SHORT 0040100D                       ; If not, return to XOR





