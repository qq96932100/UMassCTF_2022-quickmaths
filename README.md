# UMassCTF_2022-quickmaths
 使用pwntools達到腳本解
 
 ```python
from pwn import *
HOST = "34.148.103.218"
PORT = 1228

def conn():
    r = remote(HOST, PORT) 
    print(r.recvuntil(b'!'))
    r.recvline()
    r.recvline()
    return r

r = conn()
count = 1

while count <= 1000:
    try:        
        print('{0}/1000'.format(count))      
        question = r.recvline()
        print(str(question))
        break_question = question.split(b" ")
        first = int(break_question[0])
        second = int(break_question[2])
        # print('first',first)
        # print('sec',second)
        # print('symbol', break_question[1])
        if break_question[1] == b'-':
            result = str(first - second)
            print(result)
            r.sendline(result.encode())
            
        if break_question[1] == b'*':
            result = str(first * second)
            print(result)
            r.sendline(result.encode())
            
        if break_question[1] == b'+':
            result = str(first + second)
            print(result)
            r.sendline(result.encode())
            
        if break_question[1] == b'//':
            result = str(first // second)
            print(result)
            r.sendline(result.encode())
        r.recvline()
        count += 1
    except:
        r = conn() # Server side TIMEOUT 
        count = 1 # restart             
flag = r.recvline()
print(flag)
```


