**`LW dr, offset(br)`**
- given a base register and offset, put value at that memory location into destination register
# IF Stage
```
I-Mem[PC] -> FBUF
PC + 1 -> PC
```
- fetched instruction is placed into FBUF, PC incremented
# ID
```
DPRF[FBUF[BR]] -> DBUF[A]
DPRF[FBUF[Offset]] -> DBUF[B]
FBUF[DR] -> DBUF[DR]
FBUF[OPCODE] -> DBUF[OPCODE]
```
- grab value of BR, and place into dbuf
- get register specifier of dr and put into dbuf
    - register number like in project
- copy opcode to dbuf

# EX
```
DBUF[A] + DBUF[B] -> EBUF[Result]
DBUF[OPCODE] -> EBUF[OPCODE]
DBUF[DR] -> EBUF[DR]
```
- perform addition for br and offset memory address value
- copy opcode, and dr specifier

# MEM
```
Mem[EBUF[Result]] -> MBUF[Result]
EBUF[OPCODE] -> MBUF[OPCODE]
EBUF[DR] -> MBUF[DR]
```
- grab value at base + offset memory address
- copy opcode and dr specifier

# WB
```
MBUF[Result] -> DPRF[MBUF[DR]]
```
- write value at base + offset memory address into destination register