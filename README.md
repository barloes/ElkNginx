```
docker-compose up

docker run -p 8882:8882 -v $(pwd)/nginx-conf:/etc/nginx/nginx.conf \
     -v $(pwd)/nginx-site:/etc/nginx/sites-enabled/default \
     nginx nginx-debug -g 'daemon off;'
```
# ElkNginx
