# Programming Assignment 1
## Di Jin dxj170930
#### All the result are computed on 1 node and 24 cores(48 logical cores). And they are mean of 30 times run:
#### Result table of request number:1000000
Thread Number | Bakery(ms) | Tournament(ms)
---|---|---
2 | 648.43 |594.47 
4 | 853.27 |761.83 
8 | 967.47 | 807.5
16 | 1493.87 | 806.97
32 | 7591.77 | 927.97
48 | 84860.57 | 1286.9
![image](https://note.youdao.com/yws/public/resource/983606749d0689091ef1e753eff71ad4/xmlnote/6FC175F10B9F4812A024A4CE6B8107B1/269)
#### Result table of request number:100000
Thread Number | Bakery(ms) | Tournament(ms)
---|---|---
2 | 47.47 |56.7 
4 | 57.5 |62.27
8 | 62.73 | 76.2
16 | 98.07 | 88.13
32 | 979.67 | 87.003
48 | 5741 | 147.43
![image](https://note.youdao.com/yws/public/resource/983606749d0689091ef1e753eff71ad4/xmlnote/177B690105E34756B510B35141011D15/276)
#### Result table of request number:10000
Thread Number | Bakery(ms) | Tournament(ms)
---|---|---
2 | 6.43 |7.93
4 | 10.23 |8.06
8 | 12.67 | 9.27
16 | 13.07 | 9.83
32 | 105.8 | 10.76
48 | 617.9 | 15.83
![image](https://note.youdao.com/yws/public/resource/983606749d0689091ef1e753eff71ad4/xmlnote/84AABFB9F3E74749AA907F5EB58F6FA5/280)

#### Graph with thread number from 1 to 48:
![image](https://note.youdao.com/yws/public/resource/983606749d0689091ef1e753eff71ad4/xmlnote/71B4E656770C46DB823C6510D39BB5B2/285)