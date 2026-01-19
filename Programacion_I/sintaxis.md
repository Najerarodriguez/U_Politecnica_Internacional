```python
texto = "Hola mundo 23 de abril del 2026"

1  len(texto)
2  texto.upper()
3  texto.lower()
4  texto.title()
5  texto.capitalize()
6  texto.swapcase()
7  texto.casefold()
8  texto.strip()
9  texto.lstrip()
10 texto.rstrip()
11 texto.replace("o","a")
12 texto.replace("Hola","Adios")
13 texto.split()
14 texto.split(" ")
15 texto.split("o")
16 texto.rsplit()
17 texto.partition(" ")
18 texto.rpartition(" ")
19 texto.find("m")
20 texto.rfind("o")
21 texto.index("H")
22 texto.rindex("o")
23 texto.count("o")
24 texto.startswith("Hola")
25 texto.endswith("mundo")
26 texto.isalpha()
27 texto.isalnum()
28 texto.isascii()
29 texto.isdecimal()
30 texto.isdigit()
31 texto.isidentifier()
32 texto.islower()
33 texto.isnumeric()
34 texto.isprintable()
35 texto.isspace()
36 texto.istitle()
37 texto.isupper()
38 texto.center(20)
39 texto.ljust(20)
40 texto.rjust(20)
41 texto.zfill(20)
42 texto.expandtabs()
43 texto.encode()
44 texto.encode("utf-8")
45 texto.removeprefix("Hola")
46 texto.removesuffix("mundo")
47 texto.format()
48 "{}".format(texto)
49 f"{texto}"
50 texto[0]
51 texto[-1]
52 texto[0:4]
53 texto[5:]
54 texto[:4]
55 texto[::2]
56 texto[::-1]
57 texto + "!"
58 texto * 2
59 " ".join(texto)
60 "".join(texto.split())
61 list(texto)
62 tuple(texto)
63 set(texto)
64 sorted(texto)
65 "".join(sorted(texto))
66 any(texto)
67 all(texto)
68 max(texto)
69 min(texto)
70 enumerate(texto)
71 list(enumerate(texto))
72 texto.__len__()
73 texto.__add__("!!!")
74 texto.__mul__(3)
75 texto.__contains__("Hola")
76 texto.__getitem__(1)
77 texto.__iter__()
78 texto.__reversed__()
79 hash(texto)
80 id(texto)
81 type(texto)
82 isinstance(texto,str)
83 texto.translate(str.maketrans("o","a"))
84 str.maketrans("o","a")
85 texto.encode(errors="ignore")
86 texto.encode(errors="replace")
87 texto.splitlines()
88 texto.join(["Hola","mundo"])
89 texto.find("Hola",0)
90 texto.find("o",3)
91 texto.count("o",0,5)
92 texto.startswith("H",0)
93 texto.endswith("o",0,len(texto))
94 texto.center(30,"-")
95 texto.ljust(30,"-")
96 texto.rjust(30,"-")
97 texto.replace(" ","_")
98 texto.split(maxsplit=1)
99 texto.rsplit(maxsplit=1)
100 texto.partition("o")
101 texto.rpartition("o")
102 texto.capitalize().swapcase()
103 texto.upper().lower()
104 texto.strip().split()
105 texto.encode().decode()
106 texto
107 texto.format_map({})
108 texto.__format__("")
109 texto.__str__()
110 texto.__repr__()
111 texto.__eq__("Hola mundo")
112 texto.__ne__("hola mundo")
113 texto.__lt__("Z")
114 texto.__gt__("A")
115 texto.__le__("Hola mundo")
116 texto.__ge__("Hola")
117 texto.replace("mundo","Python")
118 texto.split("u")
119 texto.find(" ")
120 texto.count(" ")

# 121 números
[n for n in texto if n.isdigit()]

# 122 vocales
[c for c in texto if c.lower() in "aeiou"]

# 123 objeto abstracto
from abc import ABC; obj = ABC()

# 124 objeto con atributos
class A: pass; a=A(); a.x=1

# 125 objeto con atributos y métodos
class B:
    def __init__(self,x): self.x=x
    def f(self): return self.x

# 126 función abstracta
from abc import abstractmethod
@abstractmethod
def f(): pass

# 127 función
def suma(a,b): return a+b

# 128 diccionario
dic = {"a":1,"b":2}

# 129 matriz
mat = [[i for i in range(3)] for _ in range(3)]

# 10 básicos extra
range(5)
for i in texto: pass
if "Hola" in texto: pass
while False: pass
len(dic)
dic.keys()
dic.values()
try: int("x")
except: pass
import math
help(str)
```
