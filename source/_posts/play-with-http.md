Play with HTTP
================


## HTTP in Networking protocol Stack
  ![](./img/Network-Stack-Models1.png)
  [TCP/IP Network Programming Design Pattens in C++](https://vichargrave.github.io/articles/2013-02/tcp-ip-network-programming-design-patterns-in-cpp)

## Play with others
  * dns
  ```
  host google.com
  nslookup renrendai.com
  ```
  dns && ip.src=[localhost's ip] on wireshark to filter the packets
  * ssh
  ```
  ssh frontend@172.16.3.27
  ```
  * ping
  ```
  ping www.renrendai.com
  ```
  * NTP on UDP
  ```
  echo "Hello world" > /dev/udp/127.0.0.1/8080
  ```

## Play with HTTP
1. as a client

  set up a simple server first
  * HEAD method
    ```
    HEAD / HTTP/1.1
    Host: localhost
    ```
  * method or http version not support
    ```
    OPTIONS / HTTP/1.1
    Host: localhost

    GET / HTTP/2.0
    Host: localhost
    ```
  * make a request to get standard http response
    ```
    telnet localhost 8000
    ```
2. as a server
  * make a response tell browser to redirect
    ```
    HTTP/1.1 307 Temporary Redirect
    Location: https://www.renrendai.org/
    ```
  * a response to tell not exist
    ```
    HTTP/1.1 404 Not found
    Content-Type: text/html; charset=UTF-8

    <html>
      <head><title>Not found</title></head>
      <body>Can't find the resource.</body>
    </html>
    ```
  * make a normal response
    ```
    HTTP/1.1 200 OK
    Content-type: text/plain
    Content-length: 50

    Hello, browser! I am a real HTTP server, honestly!
    ```
3. try some real website
  * get 3xx status code
  ```
  telnet google.com 80
  ```

## Command line tools
  * http-server #node module
  * python -m SimpleHTTPServer
  * python3 -m http.server 8000
  * ncat -l 8080 && ncat localhost 8080 # use wireshark to capture the communication
  * dig, host or nslookup #lookup address or domain name from DNS
  * echo "Hello world" > /dev/udp/127.0.0.1/3000 # send data using UDP
  * [Swiss File Knife](https://sourceforge.net/projects/swissfileknife/files/1-swissfileknife/1.8.8/)



## Reference
  * [HTTP概述](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Overview)
  * [What's in an HTTP request](http://rve.org.uk/dumprequest)
  * [An easy way to send UDP packets in Linux](https://afterthoughtsoftware.com/posts/an-easy-way-to-send-udp-packets-in-linux)
  * [HTTP request methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)
  * [HTTP/1.1: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)
