# Part I : Learn

🌞 Utiliser file pour déterminer le type de : ls, ip, un fichier .mp3

```
[fmaxance@vbox ~]$ file /usr/bin/ls
/usr/bin/ls: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=1afdd52081d4b8b631f2986e26e69e0b275e159c, for GNU/Linux 3.2.0, stripped
```

```
[fmaxance@vbox ~]$ file /sbin/ip
/sbin/ip: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=77a2f5899f0529f27d87bb29c6b84c535739e1c7, for GNU/Linux 3.2.0, stripped
```

```
[fmaxance@vbox ~]$ file monsupermp3.mp3
monsupermp3.mp3: Audio file with ID3 version 2.4.0, contains:MPEG ADTS, layer III, v1, 192 kbps, 44.1 kHz, Stereo
```

🌞 Utiliser readelf sur le programme ls

```
[fmaxance@vbox ~]$ readelf -h /usr/bin/ls
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              DYN (Shared object file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x6b10
  Start of program headers:          64 (bytes into file)
  Start of section headers:          139032 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         13
  Size of section headers:           64 (bytes)
  Number of section headers:         30
  Section header string table index: 29
```

```
[fmaxance@vbox ~]$ readelf -S /usr/bin/ls
There are 30 section headers, starting at offset 0x21f18:

Section Headers:
  [Nr] Name              Type             Address           Offset
       Size              EntSize          Flags  Link  Info  Align
  [ 0]                   NULL             0000000000000000  00000000
       0000000000000000  0000000000000000           0     0     0
  [ 1] .interp           PROGBITS         0000000000000318  00000318
       000000000000001c  0000000000000000   A       0     0     1
  [ 2] .note.gnu.pr[...] NOTE             0000000000000338  00000338
       0000000000000030  0000000000000000   A       0     0     8
  [ 3] .note.gnu.bu[...] NOTE             0000000000000368  00000368
       0000000000000024  0000000000000000   A       0     0     4
  [ 4] .note.ABI-tag     NOTE             000000000000038c  0000038c
       0000000000000020  0000000000000000   A       0     0     4
  [ 5] .gnu.hash         GNU_HASH         00000000000003b0  000003b0
       0000000000000040  0000000000000000   A       6     0     8
  [ 6] .dynsym           DYNSYM           00000000000003f0  000003f0
       0000000000000bb8  0000000000000018   A       7     1     8
  [ 7] .dynstr           STRTAB           0000000000000fa8  00000fa8
       00000000000005c5  0000000000000000   A       0     0     1
  [ 8] .gnu.version      VERSYM           000000000000156e  0000156e
       00000000000000fa  0000000000000002   A       6     0     2
  [ 9] .gnu.version_r    VERNEED          0000000000001668  00001668
       00000000000000c0  0000000000000000   A       7     2     8
  [10] .rela.dyn         RELA             0000000000001728  00001728
       0000000000001410  0000000000000018   A       6     0     8
  [11] .rela.plt         RELA             0000000000002b38  00002b38
       00000000000009d8  0000000000000018  AI       6    24     8
  [12] .init             PROGBITS         0000000000004000  00004000
       000000000000001b  0000000000000000  AX       0     0     4
  [13] .plt              PROGBITS         0000000000004020  00004020
       00000000000006a0  0000000000000010  AX       0     0     16
  [14] .plt.sec          PROGBITS         00000000000046c0  000046c0
       0000000000000690  0000000000000010  AX       0     0     16
  [15] .text             PROGBITS         0000000000004d50  00004d50
       0000000000012532  0000000000000000  AX       0     0     16
  [16] .fini             PROGBITS         0000000000017284  00017284
       000000000000000d  0000000000000000  AX       0     0     4
  [17] .rodata           PROGBITS         0000000000018000  00018000
       0000000000004dec  0000000000000000   A       0     0     32
  [18] .eh_frame_hdr     PROGBITS         000000000001cdec  0001cdec
       000000000000056c  0000000000000000   A       0     0     4
  [19] .eh_frame         PROGBITS         000000000001d358  0001d358
       0000000000002128  0000000000000000   A       0     0     8
  [20] .init_array       INIT_ARRAY       0000000000020f70  0001ff70
       0000000000000008  0000000000000008  WA       0     0     8
  [21] .fini_array       FINI_ARRAY       0000000000020f78  0001ff78
       0000000000000008  0000000000000008  WA       0     0     8
  [22] .data.rel.ro      PROGBITS         0000000000020f80  0001ff80
       0000000000000a98  0000000000000000  WA       0     0     32
  [23] .dynamic          DYNAMIC          0000000000021a18  00020a18
       0000000000000210  0000000000000010  WA       7     0     8
  [24] .got              PROGBITS         0000000000021c28  00020c28
       00000000000003c8  0000000000000008  WA       0     0     8
  [25] .data             PROGBITS         0000000000022000  00021000
       0000000000000278  0000000000000000  WA       0     0     32
  [26] .bss              NOBITS           0000000000022280  00021278
       00000000000012c0  0000000000000000  WA       0     0     32
  [27] .gnu_debuglink    PROGBITS         0000000000000000  00021278
       0000000000000020  0000000000000000           0     0     4
  [28] .gnu_debugdata    PROGBITS         0000000000000000  00021298
       0000000000000b58  0000000000000000           0     0     1
  [29] .shstrtab         STRTAB           0000000000000000  00021df0
       0000000000000128  0000000000000000           0     0     1
Key to Flags:
  W (write), A (alloc), X (execute), M (merge), S (strings), I (info),
  L (link order), O (extra OS processing required), G (group), T (TLS),
  C (compressed), x (unknown), o (OS specific), E (exclude),
  l (large), p (processor specific)
```

```
[fmaxance@vbox ~]$ readelf -S /usr/bin/ls | grep ".text"
  [15] .text             PROGBITS         0000000000004d50  00004d50
```

🌞 Utiliser ldd sur le programme ls

```
[fmaxance@vbox ~]$ ldd /usr/bin/ls
        linux-vdso.so.1 (0x00007ffef39c7000)
        libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f4de26f0000)
        libcap.so.2 => /lib64/libcap.so.2 (0x00007f4de26e6000)
        libc.so.6 => /lib64/libc.so.6 (0x00007f4de2400000)
        libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007f4de264a000)
        /lib64/ld-linux-x86-64.so.2 (0x00007f4de2747000)
```

La librairie ``Glibc`` est : ``libc.so.6 => /lib64/libc.so.6``

🌞 Donner le nom ET l'identifiant unique d'un syscall qui permet à un processus de...

``lire un fichier stocké sur disque`` :

```
Nom du syscall : read
Identifiant unique : 0
```

``écrire dans un fichier stocké sur disque`` :

```
Nom du syscall : write
Identifiant unique : 1
```

``lancer un nouveau processus`` :

```
Nom du syscall : execve
Identifiant unique : 59
```

🌞 Utiliser objdump sur la commande ls

``afficher le contenu de la section .text`` :

```
[fmaxance@vbox ~]$ objdump -d /usr/bin/ls -j .text | head -n 20

/usr/bin/ls:     file format elf64-x86-64


Disassembly of section .text:

0000000000004d50 <_obstack_begin@@Base-0xb090>:
    4d50:       50                      push   %rax
    4d51:       e8 da f9 ff ff          callq  4730 <abort@plt>
    4d56:       e8 d5 f9 ff ff          callq  4730 <abort@plt>
    4d5b:       e8 d0 f9 ff ff          callq  4730 <abort@plt>
    4d60:       e8 cb f9 ff ff          callq  4730 <abort@plt>
    4d65:       e8 c6 f9 ff ff          callq  4730 <abort@plt>
    4d6a:       e8 c1 f9 ff ff          callq  4730 <abort@plt>
    4d6f:       e8 bc f9 ff ff          callq  4730 <abort@plt>
    4d74:       e8 b7 f9 ff ff          callq  4730 <abort@plt>
    4d79:       e8 b2 f9 ff ff          callq  4730 <abort@plt>
    4d7e:       66 90                   xchg   %ax,%ax
    4d80:       f3 0f 1e fa             endbr64
    4d84:       41 57                   push   %r15
```

``mettez en évidence quelques lignes qui contiennent l'instruction call`` :

```
[fmaxance@vbox ~]$ objdump -d /usr/bin/ls | grep "call" | head -n 20
    4014:       ff d0                   callq  *%rax
0000000000004a00 <calloc@plt>:
    4a04:       f2 ff 25 d5 d3 01 00    bnd jmpq *0x1d3d5(%rip)        # 21de0 <calloc@GLIBC_2.2.5>
    4d51:       e8 da f9 ff ff          callq  4730 <abort@plt>
    4d56:       e8 d5 f9 ff ff          callq  4730 <abort@plt>
    4d5b:       e8 d0 f9 ff ff          callq  4730 <abort@plt>
    4d60:       e8 cb f9 ff ff          callq  4730 <abort@plt>
    4d65:       e8 c6 f9 ff ff          callq  4730 <abort@plt>
    4d6a:       e8 c1 f9 ff ff          callq  4730 <abort@plt>
    4d6f:       e8 bc f9 ff ff          callq  4730 <abort@plt>
    4d74:       e8 b7 f9 ff ff          callq  4730 <abort@plt>
    4d79:       e8 b2 f9 ff ff          callq  4730 <abort@plt>
    4dbc:       e8 6f fb ff ff          callq  4930 <strrchr@plt>
    4dea:       e8 61 f9 ff ff          callq  4750 <strncmp@plt>
    4e05:       e8 46 f9 ff ff          callq  4750 <strncmp@plt>
    4e3c:       e8 1f fd ff ff          callq  4b60 <setlocale@plt>
    4e4b:       e8 20 fa ff ff          callq  4870 <bindtextdomain@plt>
    4e53:       e8 d8 f9 ff ff          callq  4830 <textdomain@plt>
    4e69:       e8 02 24 01 00          callq  17270 <_obstack_memory_used@@Base+0x6960>
    4eb2:       e8 e9 f8 ff ff          callq  47a0 <isatty@plt>
```

``mettez en évidence quelques lignes qui contiennent l'instruction syscall`` :

```
[fmaxance@vbox ~]$ objdump -d /usr/bin/ls | grep "syscall"
[fmaxance@vbox ~]$
```

🌞 Utiliser objdump sur la librairie Glibc

``mettez en évidence quelques lignes qui contiennent l'instruction syscall`` :

```
[fmaxance@vbox ~]$ objdump -d /lib64/libc.so.6 | grep -A 2 -B 2 "syscall" | head -n 25
   295f0:       31 ff                   xor    %edi,%edi
   295f2:       89 d0                   mov    %edx,%eax
   295f4:       0f 05                   syscall
   295f6:       eb f8                   jmp    295f0 <__libc_start_call_main+0xa0>
   295f8:       31 ff                   xor    %edi,%edi
--
000000000003e730 <__restore_rt>:
   3e730:       48 c7 c0 0f 00 00 00    mov    $0xf,%rax
   3e737:       0f 05                   syscall
   3e739:       0f 1f 80 00 00 00 00    nopl   0x0(%rax)

--
   3e7f6:       41 ba 08 00 00 00       mov    $0x8,%r10d
   3e7fc:       b8 0d 00 00 00          mov    $0xd,%eax
   3e801:       0f 05                   syscall
   3e803:       48 3d 00 f0 ff ff       cmp    $0xfffffffffffff000,%rax
   3e809:       0f 87 f1 00 00 00       ja     3e900 <__libc_sigaction+0x1c0>
--
   3e960:       f3 0f 1e fa             endbr64
   3e964:       b8 3e 00 00 00          mov    $0x3e,%eax
   3e969:       0f 05                   syscall
   3e96b:       48 3d 01 f0 ff ff       cmp    $0xfffffffffffff001,%rax
   3e971:       73 01                   jae    3e974 <kill+0x14>
--
   3e994:       be 08 00 00 00          mov    $0x8,%esi
```

``trouvez l'instrution syscall qui exécute le syscall close()`` :

```
[fmaxance@vbox ~]$ objdump -d /lib64/libc.so.6 | grep -A 2 -B 2 "syscall" | grep "0x3,%eax" -A 3
   94b64:       b8 03 00 00 00          mov    $0x3,%eax
   94b69:       0f 05                   syscall
   94b6b:       48 3d 00 f0 ff ff       cmp    $0xfffffffffffff000,%rax
   94b71:       77 05                   ja     94b78 <mq_close@@GLIBC_2.34+0x18>
--
   fe230:       b8 03 00 00 00          mov    $0x3,%eax
   fe235:       0f 05                   syscall
   fe237:       48 3d 00 f0 ff ff       cmp    $0xfffffffffffff000,%rax
   fe23d:       77 41                   ja     fe280 <__close+0x60>
--
   fe254:       b8 03 00 00 00          mov    $0x3,%eax
   fe259:       0f 05                   syscall
   fe25b:       48 3d 00 f0 ff ff       cmp    $0xfffffffffffff000,%rax
   fe261:       77 35                   ja     fe298 <__close+0x78>
--
  102774:       b8 03 00 00 00          mov    $0x3,%eax
  102779:       0f 05                   syscall
  10277b:       48 3d 00 f0 ff ff       cmp    $0xfffffffffffff000,%rax
  102781:       77 05                   ja     102788 <__close_nocancel+0x18>
--
  127c06:       b8 03 00 00 00          mov    $0x3,%eax
  127c0b:       0f 05                   syscall
  127c0d:       c3                      retq
  127c0e:       66 90                   xchg   %ax,%ax
--
  127cc2:       b8 03 00 00 00          mov    $0x3,%eax
  127cc7:       0f 05                   syscall
  127cc9:       b8 ff ff ff ff          mov    $0xffffffff,%eax
  127cce:       eb cf                   jmp    127c9f <__netlink_open+0x8f>
--
  127e95:       b8 03 00 00 00          mov    $0x3,%eax
  127e9a:       8b 7d a0                mov    -0x60(%rbp),%edi
  127e9d:       0f 05                   syscall
  127e9f:       48 8b 45 c8             mov    -0x38(%rbp),%rax
```

# Part II : Observe

