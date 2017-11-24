
all: build
	-docker stop cache
	-docker rm cache
	docker run --name cache -d -v /tmp/sandbox/content:/usr/share/nginx/html:ro -p 8080:80 nginx:1.12.1-mc

build:
	docker build -t nginx:1.12.1-mc .
