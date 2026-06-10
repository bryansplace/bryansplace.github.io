---
layout: post
title: Wake on LAN python script
date: 2024-10-10
---
Need to wake up urville secondary (old server) from outside home.

static ip : 192.168.1.111
mac       : 8a:73:92:a3:39:f2

Using Termux on phone
pip install wakeonlan
```
python
>>> from wakeonlan import send_magic_packet
>>>send_magic_packet('20:CF:30:CA:73:B9')
```

It is difficult remember mac address, so set up a script wol.py
```
from wakeonlan import send_magic_packet
send_magic_packet('20:CF:30:CA:73:B9')
```

to run ```python wol.py```

