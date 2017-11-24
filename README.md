# Nginx Cache Example
A simple Docker based demonstration of using `proxy_cache_bypass` to refresh an
object in the Nginx cache backend.

## Running
You'll need to build the images first:

- `cd frontend; make build`
- `cd backend; make build`
- `docker-compose up`

## Testing
Now simply hit `localhost:8080` and you should see the contents of
`backend/index.html`:

```
curl -sv http://localhost:8080/
```

Looking at the headers from this request you'll see:

```
*   Trying ::1...
* TCP_NODELAY set
* Connected to localhost (::1) port 8080 (#0)
> GET / HTTP/1.1
> Host: localhost:8080
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< Server: nginx/1.12.1
< Date: Fri, 24 Nov 2017 03:31:48 GMT
< Content-Type: text/html
< Content-Length: 10
< Connection: keep-alive
< Last-Modified: Fri, 24 Nov 2017 03:26:20 GMT
< ETag: "5a17915c-a"
< Expires: Sat, 24 Nov 2018 03:31:48 GMT
< Cache-Control: max-age=31536000
< Cache-Control: public
< X-Proxy-Cache: HIT
< Accept-Ranges: bytes
<
{ [10 bytes data]
* Connection #0 to host localhost left intact
Hello v8
```

Notice the `X-Proxy-Cache` registers a `HIT` (perhaps not on the first run for
obvious reasons). Now change the contents of `backend/index.html` and run the following cURL:

```
curl -svH 'Cache-Control: 1' http://localhost:8080/
```

The value of the `Cache-Control` header can be anything other than `""` or `0`.
The feedback you'll see is:

```
*   Trying ::1...
* TCP_NODELAY set
* Connected to localhost (::1) port 8080 (#0)
> GET / HTTP/1.1
> Host: localhost:8080
> User-Agent: curl/7.54.0
> Accept: */*
> Cache-Control: 1
>
< HTTP/1.1 200 OK
< Server: nginx/1.12.1
< Date: Fri, 24 Nov 2017 03:34:19 GMT
< Content-Type: text/html
< Content-Length: 10
< Connection: keep-alive
< Last-Modified: Fri, 24 Nov 2017 03:26:20 GMT
< ETag: "5a17915c-a"
< Expires: Sat, 24 Nov 2018 03:34:19 GMT
< Cache-Control: max-age=31536000
< Cache-Control: public
< X-Proxy-Cache: BYPASS
< Accept-Ranges: bytes
<
{ [10 bytes data]
* Connection #0 to host localhost left intact
<your changes here>
```

Now run the first command again, and you'll get a `HIT` again, but the content
will have changed. This is because Nginx bypassed the cache in the second
request and noticed the content of the file had changed, so it updated its
cache to reflect changes.
