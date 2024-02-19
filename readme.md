# nginx-upload-moduleを含むnginxコンテナ

## 実行方法
`
docker run -p 8080:8080 -d irumaru/nginx-mod-nginx-upload-module:1.24.0-0.4.1
`

defaultでnginx user(非root)で実行するため、1024以上のポートを使用可能。  

## Dockerリポジトリ

[https://hub.docker.com/r/irumaru/nginx-mod-nginx-upload-module](https://hub.docker.com/r/irumaru/nginx-mod-nginx-upload-module)

## nginx upload moduleの使い方

### nginxの設定ファイル(サンプル)
```
server {
    listen 8080;

    client_max_body_size 100m;

    # Upload form should be submitted to this location
    location /files/upload {
        # Pass altered request body to this location
        upload_pass @backend;

        # Store files to this directory
        # The directory is hashed, subdirectories 0 1 2 3 4 5 6 7 8 9 should exist
        upload_store /tmp;

        # Allow uploaded files to be read only by user
        upload_store_access user:rw group:rw all:r;

        # Set specified fields in request body
        upload_set_form_field $upload_field_name.name "$upload_file_name";
        upload_set_form_field $upload_field_name.content_type "$upload_content_type";
        upload_set_form_field $upload_field_name.path "$upload_tmp_path";

        # Inform backend about hash and size of a file
        upload_aggregate_form_field "$upload_field_name.md5" "$upload_file_md5";
        upload_aggregate_form_field "$upload_field_name.size" "$upload_file_size";

        # この項目は以下の理由により、ファイル以外を全てパス(転送)するよう変更中のため、設定不要
        # https://github.com/fdintino/nginx-upload-module/issues/140#issuecomment-1192776396
        # irumaru/nginx-mod-nginx-upload-module:1.24.0-0.4.1の以降の仕様
        #upload_pass_form_field "^meta\..*";

        upload_cleanup 400 404 499 500-505;
    }

    # Pass altered request body to a backend
    location @backend {
        proxy_pass http://target_web_server_host;
    }
}
```

### webサーバー(サンプル)

```
package main

import (
	"net/http"

	"github.com/labstack/echo/v4"
	"github.com/labstack/echo/v4/middleware"
	"github.com/labstack/gommon/log"
)

func main() {
	e := echo.New()
	e.Use(middleware.Logger())
	e.Use(middleware.Recover())

	// ファイルのアップロード
	e.POST("/files/upload/:token", func(c echo.Context) error {
        token := c.Param("token")

        name := c.FormValue("file.name")
        contentType := c.FormValue("file.content_type")
        path := c.FormValue("file.path")
        md5 := c.FormValue("file.md5")
        size := c.FormValue("file.size")

        c.Logger().Debug("name: ", name, "  contentType: ", contentType, "  path: ", path, "  md5: ", md5, "  size: ", size, "  token: ", token)

        return nil
    })

	e.Logger.Fatal(e.Start(":80"))
}
```

### webアップローダー(サンプル)
```
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>アップロード/簡易テスト</title>
</head>
<body>
    <h1>アップロード/簡易テスト</h1>
    <form action="http://target_nginx_host:8080/files/upload/12345678" method="post" enctype="multipart/form-data">
        <input type="file" name="file">
        <input type="submit" value="送信">
    </form>
</body>
</html>
```
