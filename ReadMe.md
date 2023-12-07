# nginx-http-radius authentication module
    http authentication by radius protocol.

@author qudreams

# Note:

The `nginx-http-radius` module depends on
[libmyradclient](https://github.com/ten0s/libmyradclient),
so you must compile the library first;
`libmyradclient.a` must be in same directory with the `nginx-http-radius` module,
because the `nginx-http-radius` module
set the environment variables `CORE_INCS` and `CORE_LIBS` in `config` file depending this.
The module supports the following authentication methods: PAP,CHAP,MSCHAP,MSCHAPV2,EAPMD5

# Usage:

1. compile the `libmyradclient` first.

2. compile the module into nginx like this:

```
$ ./configure --add-module=src/nginx-http-radius-module
$ make
```

3. install it by the following directive:

```
$ [sudo] make install
```

Nginx will be installed into the directory /usr/local/nginx acquiescently.

4. install nginx,and then modify the configuration file `nginx.conf`.
   the configuration file may be like the following:

```
http {
    #set the directory of radius dictionary (optional, not needed).
    #radius_dict_directory "/usr/local/nginx/raddb/";

    #radius server configuration including

    radius_server "radius_server1" {
        #authentication timed-out
        auth_timeout 5;

        #limit to resend the request
        resend_limit 3;

        #radius authentication server url.
        url "127.0.0.1:1812";

        #share secret
        share_secret "secret";
    }

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location = /{
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
