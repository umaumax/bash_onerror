# bash_onerror

simple bash debugger

* あるシェルスクリプトでエラーとなった箇所の変数の値を確認しつつ，正しいコマンドを実行してから，処理を再開させることを可能にするスクリプト
  * ただし，スクリプトAからスクリプトBを`source`する形式以外で実行している場合には，そのスクリプトBは`trap`の対象外となることに注意

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

## FYI
```
$ man bash
       -c        If the -c option is present, then commands are read  from  the  first
                 non-option argument command_string.  If there are arguments after the
                 command_string, the first argument is assigned to $0 and any  remain-
                 ing arguments are assigned to the positional parameters.  The assign-
                 ment to $0 sets the name of the shell, which is used in  warning  and
                 error messages.
```
