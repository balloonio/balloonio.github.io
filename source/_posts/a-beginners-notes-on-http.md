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

An HTTP session is established on top of TCP/IP protocol with 80 as the default port. HTTP is a stateless protocol in the sense that web servers are not required to keep the status information about each client (browsers). However, some web applications do implement states or server side sessions using HTTP cookies or hidden variables within web forms.

