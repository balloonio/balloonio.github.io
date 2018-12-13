---
title: A beginner's notes on HTTP
date: 2018-12-08 23:36:13
tags:
  - front end
  - web
  - http
---

Coming from a non-CS background, HTTP always seems to be a mystery to me. As I learned more about front-end technologies, I started to realize that understanding HTTP protocal is really important. Working on a Django project without understanding how HTTP works is like writing an assembly program without understanding CPU architecture - it is very easy to get lost. Fortunately, I came across some very helpful materials. Here I want to share my study notes on those materials.

# What is HTTP?

{% blockquote Wikipedia https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Technical_overview Hypertext Transfer Protocol(HTTP) %}
HTTP functions as a request–response protocol in the client–server computing model. A web browser, for example, may be the client and an application running on a computer hosting a website may be the server. The client submits an HTTP request message to the server. The server, which provides resources such as HTML files and other content, or performs other functions on behalf of the client, returns a response message to the client. The response contains completion status information about the request and may also contain requested content in its message body.
{% endblockquote %}

&nbsp;
HTTP stands for Hypertext Transfer Protocol. What is Hypertext? Hypertext is just a fancy text whose content is not limited to text. Hypertext can contain texts, images, videos, Flash or even scripts. The most common carrier form of Hypertext is HTML (Hypertext Markup Language) files. Therefore, in shortest words, HTTP is the standard that defines how HTML contents should be transferred (between browsers and web servers in most cases). The standard specifies that hypertext should be transferred through a request-response mechanism. It also defines how requests/responses should look like.

# HTTP session

{% blockquote Wikipedia https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#HTTP_session HTTP session %}
An HTTP session is a sequence of network request-response transactions. An HTTP client initiates a request by establishing a Transmission Control Protocol (TCP) connection to a particular port on a server (typically port 80, occasionally port 8080; see List of TCP and UDP port numbers). An HTTP server listening on that port waits for a client's request message. Upon receiving the request, the server sends back a status line, such as "HTTP/1.1 200 OK", and a message of its own. The body of this message is typically the requested resource, although an error message or other information may also be returned.
{% endblockquote %}

&nbsp;
Normally, an HTTP session is established on top of an TCP/IP layer with 80 as the default port. It is commonly said to be **stateless** by textbooks. However, wether it is truly stateless or not has been suffering from furious debates. Some people say HTTP/1 is stateless and HTTP/2 is not, while others say both are stateless. Even the definition of **statelessness** itself, under this context, is not clear in the dev community. Some people believe carrying a cookie is considered stateful, some others think as long as sending the same request multiple times would give the same result, it is considered stateless, and the rest may think carrying a security key is stateful but it is considered part of the TLS/SSL but not HTTP itself. Since I'm not a professional on this ( and it is a very controversial topic ), I don't share any personal opinion here and I encourage you to read more online if interested.

However, I would like to shed some lights on the unambiguous topics that I'm comfortable with, of which I can pretty much guarantee the correctness after a lot of research. The first keyword is **persistent connection**. As mentioned earlier, HTTP sessions is normally established on TCP connections. In HTTP/0.9 and HTTP/1.0, the underlying TCP connection is closed after each request-reponse pair, meaning everytime a new HTTP request is sent, the TCP connection needs to be re-negotiated and re-established. However, in HTTP/1.1, a keep-alive-mechanism was introduced, where a connection could be reused for more than one request. The term persistent connection, though discussed in the context of HTTP, is describing the underlying TCP connection used by the HTTP request, not an HTTP session itself. There is nothing to be persistent about in an HTTP communication.