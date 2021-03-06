# ElkNginx

```
docker-compose up

docker run -p 8882:8882 -v $(pwd)/nginx-conf:/etc/nginx/nginx.conf \
     -v $(pwd)/nginx-site:/etc/nginx/sites-enabled/default \
     nginx nginx-debug -g 'daemon off;'

```

```

proxy_cache_path /tmp/nginx levels=1:2 keys_zone=STATIC:30m inactive=15m max_size=10g;
proxy_cache_key "$request_method$request_uri";

map $sent_http_etag $kibana_cache_control {
  default "";
  ~.+  public,max-age=31536000,immutable;
}

upstream kibana {
    server host.docker.internal:5601;
    keepalive 100;
}

map $sent_http_content_type $expires {
    default                    off;
    text/html                  epoch;
    text/css                   max;
    application/javascript     max;
    application/json           max;
    ~image/                    max;
}


# HTTP

server {

  listen 8882;
  listen [::]:8882;
  ex
  location / {
    proxy_pass http://kibana/;

    access_log   /var/log/nginx/access.log rt_cache;
    proxy_buffering        on;
    proxy_cache            STATIC;
    proxy_cache_methods GET HEAD POST;
    proxy_cache_revalidate on;
    proxy_cache_valid      200  30m;
    proxy_cache_use_stale  error timeout invalid_header updating
                            http_500 http_502 http_503 http_504;

    add_header X-Cache-Status $upstream_cache_status;

    proxy_http_version 1.1;
    proxy_request_buffering off;

    proxy_set_header   X-Real-IP $remote_addr;
    proxy_set_header   Host      $host;
    proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
    proxy_set_header   X-Forwarded-Proto $scheme;
    proxy_set_header   X-Forwarded-Host $host;

    proxy_hide_header "Cache-Control";
    add_header "Cache-Control" $kibana_cache_control;
  }
}


```
