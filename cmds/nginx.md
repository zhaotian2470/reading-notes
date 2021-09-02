# location
* Using the “=” modifier to define an exact match of URI and location. If an exact match is found, the search terminates.
* If the longest matching prefix location has the “^~” modifier then regular expressions are not checked.
* Regular expressions ("~" or "~*") are checked, in the order of their appearance in the configuration file. The search of regular expressions terminates on the first match, and the corresponding configuration is used.
* Nginx checks locations defined using the prefix strings (prefix locations). Among them, the location with the longest matching prefix is selected and remembered.

refer to:
[nginx location](http://nginx.org/en/docs/http/ngx_http_core_module.html#location)
