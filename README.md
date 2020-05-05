# bash_onerror

simple bash debugger

## how to use
```bash
$ ./bash_onerror.sh <(echo '#!/bin/bash\necho start\na=1\nb=2\nc=3\nd=4\nurl=www.google.com\nwgot $url\n[[ -e index.html ]] && echo success\necho end')
start
/tmp/bash_onerror.sh.18219.tmp.XcwBWs/11: line 8: wgot: command not found
=================================================================================================================================================================================================
[TRAP ERR]
[✘ 127] wgot $url
11:8
     3	a=1
     4	b=2
     5	c=3
     6	d=4
     7	url=www.google.com
     8	wgot $url
     9	[[ -e index.html ]] && echo success
    10	echo end
[new variables]
+ a=1
+ b=2
+ c=3
+ d=4
+ url=www.google.com
[prompt command]: <bash command>, q|exit|quit, c|continue
[wd] /xxx/yyy/zzz
=================================================================================================================================================================================================
> wget $url
--2020-05-06 02:14:17--  http://www.google.com/
Resolving www.google.com (www.google.com)... 172.217.31.164
Connecting to www.google.com (www.google.com)|172.217.31.164|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [text/html]
Saving to: ‘index.html’

index.html                                           [ <=>                                                                                                    ]  15.65K  --.-KB/s    in 0.008s

2020-05-06 02:14:18 (1.80 MB/s) - ‘index.html’ saved [16024]

> c
success
end
```
