# Request Counter Module for Nginx

## Disclaimer

This module was created as an example for this StackOverflow 
[question](http://stackoverflow.com/questions/41586158/nginx-stub-status-ignore-own-requests).
It was developed in very short time and was not properly tested.

**Under no circumstances must it be used in production environment.**

## Introduction

This code serves as an example of how to bypass restrictions of 
[Nginx](http://nginx.org) with simple user-defined modules. The module comes as 
an extension for the standard
[stub status](https://nginx.org/en/docs/http/ngx_http_stub_status_module.html)
module, which provides some basic Nginx metrics. By default, stub status module counts every request
to any location without exception and this behavior cannot be changed simply with configuration.

The main purpose of the Request Counter Module is to eliminate this problem by introducing the ability
to enable or disable request counting in the context of a specific [server](http://nginx.org/en/docs/http/ngx_http_core_module.html#server) 
or [location](http://nginx.org/en/docs/http/ngx_http_core_module.html#location). This feature can
be useful for adjusting monitoring tools or implementing custom statistical data collection services.

Currently, this module provides only one single directive `request_counter`:

```
Syntax:  request_counter on | off;
Default: on
Context: http, server, location
```

Since there is no way to alter the behavior of the standard sub status module without patching its
code, the idea behind this directive is to decrement the request counter by one if the request is
handled in the context, where `request_counter` is set with `off`.
The decrement happens on the last stage of the request processing (*NGX_HTTP_LOG_PHASE*), which, 
with proper configuration, even allows to change the behavor of the counter based on the response 
from the upstream.


## Installation

   1. Configure Nginx adding this module with:
          
          ./configure (...) --add-dynamic-module=/path/to/nginx-http-request-counter-module
       
   2. Build Nginx as usual with `make`.
   
   3. Enable the module by adding this line in the global context of the
      configuration file:

          load_module relative/path/to/ngx_http_request_counter_module.so;

   4. Configure the module. Example:
          
          location = /stats {
             stub_status on;
             request_counter off;
          }
   
   5. Restart Nginx.


## References

 * [Emiller's Guide To Nginx Module Development](http://www.evanmiller.org/nginx-modules-guide.html);
 * [Development of modules for nginx ](http://antoine.bonavita.free.fr/nginx_mod_dev_en.html);
 * [Nginx Discovery Journal](http://www.nginx-discovery.com/);
 * [Nginx 3rd Party Modules](https://www.nginx.com/resources/wiki/modules/index.html)
