# AGN9_Research
Research into anti-cheat software AGN9 (intrusive)

This software features anti-debugging, anti-injection, anti-tamper, spoofed exports, and more..

#Exports:
-Fakes an ordinal entry and gives it an extremely large name at RVA 0x0, possibly to try and overflow common disassemblers such as Cheat Engine. The result is that when you try to analyze this at runtime, the disassembler will attempt to load the export name (which is way too large + gibberish) and destroys the UI of the disassembler as it will reference addresses as being "that gibberish" + some offset, making it a pain to reverse at runtime.

-The solution to this is to use my ModifyExportsName project code and write over that bad export's name. We are then left with one single export named "EncryptedFunction" @1 , at RVA 0x3680. This is likely the anticheat dispatch function similar to hackshield, which is one export function being used to initialize, send packets, etc with a flag as the parameter acting as an opcode.
