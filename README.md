# AGN9_Research
Research into anti-cheat software AGN9 (intrusive)

This software features anti-debugging, anti-injection, anti-tamper, spoofed exports, and more..

Exports:
-Fakes an ordinal entry and gives it an extremely large name at RVA 0x0, possibly to try and overflow common disassemblers such as Cheat Engine. The result is that when you try to analyze this at runtime, the disassembler will attempt to load the export name (which is way too large + gibberish) and destroys the UI of the disassembler as it will reference addresses as being "that gibberish" + some offset, making it a pain to reverse at runtime.

-The solution to this is to use my ModifyExportsName project code and write over that bad export's name. We are then left with one single export named "EncryptedFunction" @1 , at RVA 0x3680. This is likely the anticheat dispatch function similar to hackshield, which is one export function being used to initialize, send packets, etc with a flag as the parameter acting as an opcode.

We must delete Ordinal2 @ AGN9.DLL + 0xBEFA94, the junk ordinal name starts with bytes "28 65 C3 86 62 C3 A8", we should attempt to edit this .DLL and remove traces of this export... stay tuned

Entrypoint
x64dbg lists entrypoint at +C01250 which is likely spoofed, we get the instruction call 0x00007FFA8914651D which leads to packed code.

Sections
Section sizes are spoofed to be 0 , and some extra sections have been added: ".[9", ".&[", ".i&J" with sizes 0, 0xe00, and 0xda3200.


Dumping process
We can dump whatever process its trying to protect by using a kernel mode driver to read and write memory. For this example we are looking at the game Aero Tales The New World. I've built a small driver controller which can dump any .exe, soon we will check if it can successfully dump the entire .sys or .dll of the anticheat. After dumping the AGN9.dll module we can't inspect it with IDA as it'll say invalid PE format, in this case there was a 1 byte difference in size in the PE header which is fixed with any binary file editor. Then upon loading into IDA, we get: "Number of exported names 1962891300 is incorrect, maximum possible value is 533. Do you want to continue with the new value?". We then get "The input file contains non-empty TLS (Thread Local Storage) callback table.
However, IDA could not find the TLS callback procedures in the loaded code.
Please reload the input file manually and load all segments.", along with "Corrupt fixup table", "Some relocations are not applied". It appears this dump is packed with Winlicense/Themida, so we should probably find a way to unpack it if we cant figure anything else out to inject code.

Hooks
This software places quite a few hooks which check for unsigned DLLs, etc. Here's a list of some of the things hooked:

RtlGetFullPathName_U
LoadLibraryA/W
OculusXRPlugin

#Code Injection
This can be done with DLL proxying (confirmed)


