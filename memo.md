## nginxが起動しない

nginxはbackgroundでdaemonで動く  
dockerはforegroundで動くプロセスがないと終了する  
そのため、以下のコマンドでforgroundで起動する必要がある  

`
CMD ["/usr/sbin/nginx", "-g", "daemon off;"]
`

## dockerfileでcdを使用

ダウンロードするライブラリのファイル名にバージョンが含まれている。
workディレクトリからフルパスを使用すると、ライブラリのバージョン変更時に変更する箇所が増える。
そのため、cdを使用している。
