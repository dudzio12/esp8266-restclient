# RestClient for Arduino ESP8266 WiFi modules

HTTP Request library for Arduino and the ESP8266 WiFi SOC modules

This library supports SSL!  
To use with SSL either include the SHA1 fingerprint of the certificate of the site you are connecting to, or force the library to use ssl insecurely (don't veryify the server is who it says it is).

You can get the SHA1 fingerprint by using a desktop browser and inspecting the SSL cert used at the site.  Please note: this is FRAGILE, if the site updates their SSL, your code will break.  But, there is not enough memory on the ESP8266 to store all the rool certs, so this is a working method.  Se the example below.

This library is derived almost entirely from the great work done here: https://github.com/csquared/arduino-restclient

# Fork note

API I used returns token inside response header, there was no way to access it, so I've managed to prepare two more functions. One for full access to raw response, and the second to fetch specific header value in simplest possible way. 
I have no time to prepare full test suites, but it should work on any environments.

# Install

Clone (or download and unzip) the repository to `~/Documents/Arduino/libraries`
where `~/Documents/Arduino` is your sketchbook directory.

    > cd ~/Documents/Arduino
    > mkdir libraries
    > cd libraries
    > git clone https://github.com/dakaz/esp8266-restclient.git RestClient

# Dependencies

You need to have the `ESP8266` board support already installed.

# Usage

Constructor to create an RestClient object to make requests against.
```c++
RestClient(const char* host/ip, [int port], [bool force / const char* fingerprint]);
```

Use a domain name and default to port 80:
```c++
RestClient client = RestClient("esp-rest-test.herokuapp.com");
```

Use a local IP and an explicit port:
```c++
RestClient client = RestClient("192.168.1.50", 5000);
```

Use a domain name, an explicit port to an SSL site and verify the certificate with its fingerprint:
```c++
RestClient client = RestClient("www.kudoso.com", 443, "EE 16 77 79 55 58 92 46 FB 18 40 99 2E 17 7E AB 32 0A 4A 88");
```

Use a domain name, an explicit port to an SSL site and force insecure SSL with no certificate verification:
```c++
RestClient client = RestClient("www.kudoso.com", 443, 1);
```

### dhcp()

Sets up `EthernetClient` with a mac address of `DEADBEEFFEED`. Returns `true` or `false` to indicate if setting up DHCP
was successful or not. Not used on the ESP.

## RESTful methods

All methods return an HTTP status code or 0 if there was an error.

Start making requests!

## GET

```c++
get(const char* path, [String* response]);
```

Examples:
```c++
int statusCode = client.get("/");
```

```c++
String response = "";
int statusCode = client.get("/", &response);
```

## POST

There are three different overloads of post:
```c++
post(const char* path, const char* body);
post(const char* path, String* response);
post(const char* path, const char* body, String* response);
```

Examples:
```c++
String response = "";
int statusCode = client.post("/", &response);
```

```c++
const char* post_body = "{foo: 'bar', bob: 'alice'}";

int statusCode = client.post("/", post_body);
```

```c++
String response = "";
const char* post_body = "{foo: 'bar', bob: 'alice'}";

int statusCode = client.post("/", post_body, &response);
```

## PUT

```c++
put(const char* path, const char* body);
put(const char* path, String* response);
put(const char* path, const char* body, String* response);
```

## DEL

```c++
del(const char* path);
del(const char* path, const char* body);
del(const char* path, String* response);
del(const char* path, const char* body, String* response);
```

## Additional function implemented in this fork

```c++
// Get raw response (without body)
getRawLastResponse();
// Get response header value by key
getResponseHeader(const char* key, String*);
```

Examples:

```c++
String rawResponse = client.getRawLastResponse();
String headerValue;
client.getResponseHeader("Token", &headerValue);
```

## Full Example

I test every way of calling the library (against a public heroku app)[https://github.com/csquared/arduino-http-test].

You can find the file in File->Examples->RestClient->full_test_suite

## Debug Mode

If you're having trouble, you can always open `RestClient.cpp` and throw at the top:

```c++
#define HTTP_DEBUG
```

Everything happening in the client will get printed to the Serial port.

# Thanks

[ricardochimal](https://github.com/ricardochimal) For all his c++ help.  Couldn't have done this without you!

[theycallmeswift](https://github.com/theycallmeswift) Helping incept and debug v1.0
