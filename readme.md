# nginx-upload-moduleを含むnginxコンテナ

## 実行方法
`
docker run -p 8080:8080 -d irumaru/nginx-mod-nginx-upload-module:1.24.0-0.2.1
`
defaultでnginx user(非root)で実行するため、1024以上のポートを使用可能。  

## Dockerリポジトリ

[https://hub.docker.com/r/irumaru/nginx-mod-nginx-upload-module](https://hub.docker.com/r/irumaru/nginx-mod-nginx-upload-module)
