---
layout: post
title: "Singleton Pattern"
description: ""
category: ['Engineer']
tags: [design pattern, software engineer]
date: 2016-02-15T20:36:10+08:00
---

Singleton Pattern may be the most familiar with for software engineers in distributed system design or web development. Most network application need only one socket in tcp/udp server as the listener of incoming network traffic. Every time data packet comes, the socket instance is used for get the data. This pattern works very naively. In order to force the user to create only one instance of certain class, the construction method of this class is declared as private and inside the class declaration there is a static member of the same class. The following code is a socket server use singleton pattern.

{% highlight cpp linenos %}
#include <iostream>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <netdb.h> 

class socket{
public:
	socket* get_instance(){
		if(socket::instance == NULL)
			socket::instance = new socket();
		return socket::instance;
	}

	bool read(){
    	n = read(sockfd, buf, 4096);
    	if(n < 0)
    		printf("Error receiving data.\n");
	}

	bool write(){
		int n = write(sockfd, buf, strlen(buf));
		if(n < 0)
			printf("Error writing.\n");
	}

	char* get_buffer(){
		return buf;
	}

	void destroy(){
		close(socket::instance->sockfd);
	}

private:
	socket(){
		port = 12345;
    	sockfd = socket(AF_INET, SOCK_STREAM, 0);
    	server = gethostbyname("localhost");
		bzero((char *) &serv_addr, sizeof(serv_addr));
	    serv_addr.sin_family = AF_INET;
	    bcopy((char *)server->h_addr, 
	         (char *)&serv_addr.sin_addr.s_addr,
	         server->h_length);
	    serv_addr.sin_port = htons(portno);
    	if (connect(sockfd,(struct sockaddr *)
    			&serv_addr,sizeof(serv_addr)) < 0) 
    		printf("Error occur! Connection failed\n");
	}
	char buf[4096];
	short port;
	int sockfd;
    struct sockaddr_in serv_addr;
	static socket* instance;
}

socket* socket::instance = NULL;
{% endhighlight %}

Obviously, the user of socket class can not access the construction, so he can't use new or initialize an object of socket hence able to use the same object each time. This can be used in many sceniariors that you need to force the single existance of certain class.
