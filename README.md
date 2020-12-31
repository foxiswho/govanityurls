
**Note: The origin GoogleCloudPlatform/govanityurls has alreay support running as a standalone server. so I advise you use the origin one. This repo will not be maintained later.


# Go Vanity URLs for normal VPS(virtual private server)

Go Vanity URLs is a simple app that allows you to set custom import paths for your Go packages. 

>Note: **This go vanity urls is forked from GoogleCloudPlatform/govanityurls. GoogleCloudPlatform/govanityurls only works on google app engine. go vanity urls here could work on normal VPS.**

## Quickstart

Install go vanity urls:

```
$ go get github.com/foxiswho/govanityurls
```

Edit `vanity.yaml` to add any number of git repos. E.g., `tonybai.com/gowechat` will
serve the [https://github.com/bigwhite/gowechat](https://github.com/bigwhite/gowechat) repo.

```
/gowechat:
  repo: https://github.com/bigwhite/gowechat

/x/experiments:
  repo: https://github.com/bigwhite/experiments
```
You can add as many rules as you wish.

>Before run the app, point your custom domain to the vps ip where govanityurl deployed. 

govanityurls listens on address "0.0.0.0:8080" (or custom port) as default. It is better to use a reverse proxy to transfer the real go get requests because you may have other services under your domain. Below is a nginx config example on ubuntu 16.04:

```
// /etc/nginx/conf.d/default.conf
server {
        listen 80;
        listen 443 ssl;
        server_name tonybai.com;

        ssl_certificate           /etc/nginx/cert.crt;
        ssl_certificate_key       /etc/nginx/cert.key;
        ssl on;

        location /gowechat {
                proxy_pass http://10.11.36.23:8080;
                proxy_redirect off;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection "upgrade";
        }
}
```

Run the app:

```
$ cd $(go env GOPATH)/src/github.com/foxiswho/govanityurls
$ govanityurls -host tonybai.com

OR

$ govanityurls -host tonybai.com -port 8080
```

That's it! You can use `go get` to get the package from your custom domain.

```
$ go get tonybai.com/gowechat
```
