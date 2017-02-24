## 简介

nginx-upsync-module - Nginx C module, sync upstreams from consul、etcd and **zookeeper**, dynamiclly modify backend-servers attribute(weight, max_fails,...), needn't reload nginx.

nginx插件，支持在不reload nginx的情况下，更新upstream配置。upstream配置存储在etcd/zk/consul中.

因为zk不提供内置的rest http api，因此要使用[ezbz/Zookie
](https://github.com/ezbz/Zookie)，本插件的json格式解析也是按照[ezbz/Zookie
](https://github.com/ezbz/Zookie)来的。



	upstream test {
        # fake server otherwise ngx_http_upstream will report error when startup
        server 127.0.0.1:11111;

        # all backend server will pull from consul when startup and will delete fake server
        upsync 192.168.3.56:12023/zookie/tree?root=/nginx/upstreams/test upsync_timeout=6m upsync_interval=500ms upsync_type=zk strong_dependency=off;
        upsync_dump_path /usr/local/nginx/conf/servers/servers_test.conf;
    }

    server {
        listen 90;

        location = /proxy_test {
            proxy_pass http://test;
        }

        location = /upstream_show {
            upstream_show;
        }

    }

	


