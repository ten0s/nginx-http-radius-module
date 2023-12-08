# nginx-http-radius authentication module

HTTP Authentication by Radius protocol.

@author qudreams

# Note:

The `nginx-http-radius` module depends on [libmyradclient](https://github.com/ten0s/libmyradclient).<br>
The module supports the following authentication methods: PAP,CHAP,MSCHAP,MSCHAPV2,EAPMD5

# Usage:

## Build

```
$ cd /path/to/build/dir
```

### Clone and compile `libmyradclient`:

```
$ git clone https://github.com/ten0s/libmyradclient.git
$ cd libmyradclient
$ make
$ cd ..
```

### Clone `nginx-http-radius-module`:

```
$ clone https://github.com/ten0s/nginx-http-radius-module.git
```

### Download Nginx:

```
$ export NGINX_VERSION=1.20.1
$ wget http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz
$ tar xfz nginx-${NGINX_VERSION}.tar.gz
```

### Compile the static module:

```
$ cd nginx-${NGINX_VERSION}
$ ./configure --add-module=../nginx-http-radius-module
$ make
```

### Compile the dynamic module:

For dynamic modules to be loaded correctly the downloaded Nginx version and
Nginx build options must match exactly the target Nginx installation:

```
$ nginx -V 2>&1 | egrep  "^configure" | cut -d: -f2 > nginx_build_opts.txt
$ bash -c "./configure --add-dynamic-module=../nginx-http-radius-module $(cat nginx_build_opts.txt)"
$ make
```

### Optionally install Nginx:

```
$ [sudo] make install
```

Nginx will be installed into the directory /usr/local/nginx acquiescently.

### Configuration:

Install nginx, and then modify the configuration file `nginx.conf`.
   the configuration file may be like the following:

```
# Load the dynamic module
# load_module /usr/lib64/nginx/modules/ngx_http_auth_radius_module.so;

http {
    # Radius server configuration
    radius_server "radius_server1" {
        # Authentication timed-out
        auth_timeout 5;

        # Limit to resend the request
        resend_limit 3;

        # Radius authentication server url
        url "127.0.0.1:1812";

        # Share secret
        share_secret "secret";
    }

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location = / {
            root   html;
            index  index.html index.htm;

            #radius server configuration

            #the third paramter is authentication method,you can set the following value:
            # PAP CHAP MSCHAP MSCHAP2 EAPMD5

            auth_radius_server "radius_server1" "PAP";

            #authentication realm,you can set the following value:
            # Restricted "Close Content" off

            auth_radius "Restricted";
        }
    }
}
```
