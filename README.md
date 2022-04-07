# UMassCTF_2022-quickmaths
 使用pwntools達到腳本解
 <h2>Question:</h2>
nc 34.148.103.218:1228，solve 1000 of math problems. Easy or hard? Up to you.

<h2>Solve:</h2>
1. 用netcat連上server uwu

```linux
nc 34.148.103.218:1228
```

會得到：

```linux
You must solve 1000 of these math problems that are outputted in the following format {number} {operation} {number} to get the flag. 
Division is integer division using the // operator. 
The input is being checked through python input() function. 
Good luck! 

96 * 51
```

<h3>分析題目</h3>

server會隨機出數學題，並透過input()做輸入。
<h4>這代表什麼?</h4>
我們的任務就是想辦法取得<b>一行一行的題目</b>，並對題目做運算後直接回傳給server就可以解決一題了；只要循環這個過程1000次，我想就能得到Flag了，耶。
聽起來真簡單:)

<p></p>
<img src="https://memeprod.sgp1.digitaloceanspaces.com/user-resource-thumbnail/dd3e37a8c6da1078cd1e580ddd5cf5fc.png">

<h3>pwntools撰寫:</h3>

 ```python
from pwn import *
HOST = "34.148.103.218"
PORT = 1228

def conn(): #connect to server
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
<h2>腳本運行過程:</h2>
<img src="https://github.com/qq96932100/UMassCTF_2022-quickmaths/blob/main/img/script_running.png"/>

<h2>腳本運行結果:</h2>
<img src="https://github.com/qq96932100/UMassCTF_2022-quickmaths/blob/main/img/flag.png"/>

<h2>有遇到甚麼困難?</h2>
在解這題的過程中，常常會跳出EOFError，似乎是server timeout了。  
所以我def了連線方式，在timeout後可以直接重新連線再跑一次。
<img src="https://backstage.headout.com/wp-content/uploads/2021/04/ezgif-2-423490eb1f31.gif" alt="this_is_fine.gif">
