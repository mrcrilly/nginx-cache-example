FROM nginx:1.12.1
RUN apt update && apt install vim git -y
RUN git clone https://github.com/mrcrilly/vim.git /root/vim
RUN cp -r /root/vim/dotvim /root/.vim
RUN mkdir -p /data/nginx/cache
COPY nginx.conf /etc/nginx/nginx.conf
COPY default.conf /etc/nginx/conf.d/default.conf

