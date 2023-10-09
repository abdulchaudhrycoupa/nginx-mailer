# Nginx Mail Handler

Run this locally for debugging.

```bash
./build/nginx-1.25.2/objs/nginx -e error.log -p $(pwd) -c nginx.conf -g "pid nginx.pid; daemon off;"
```

