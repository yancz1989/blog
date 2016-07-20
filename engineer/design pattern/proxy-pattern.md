---
layout: post
title: "Proxy Pattern"
description: ""
category: ['Engineer']
tags: [design pattern, software engineer]
date: 2016-02-17T00:35:44+08:00
---
Proxy pattern should be a familiar pattern if you have see some details in a distributed system. In that case, the object you want to keep watching may be a remote object, so the state of the object should be the remote one. You need a proxy to handle the state synchronization so that each time you check the object, the proxy communicate with the remote one and obtain the remote state, hence it's the remote state for the viewer in local. Also, the feature of remote method invocation(RMI) or the remote procedure call(RPC) is accomplished with proxy. When you call the local object to handle something, the proxy acturally push the message to the remote helper, and let remote object handle it. Like the picture shows below.


![Alt text](/assets/images/engineer/20160217_proxy_pattern/figure_1.png "图1")

In front the users, your local object communicate with the remote object throught the local helper and remote helper. Both state check and RMI is done with the help of helper object. Your code should at least implement class of message or command(command pattern), a command generate(factory pattern), a socket(singleton pattern) for communication.

{% highlight cpp linenos %}
enum packet_type{
	command,
	data
}

class packet{
public:
	void set_data(void* dat, unsigned int l){
		unsigned char* tmp = reinterpret_cast<unsigned char*>(dat);
		data.resize(l);
		for(int i = 0; i < l; ++i)
			data[i] = tmp[i];
	}
	vector<unsigned char>& get_data(){
		return data;
	}
private:
	vector<unsigned char> data;
	packet_type type;
}

enum command_type{
	check,
	change,
	close
}

class command:public: packet{
public:
	virtual bool execute() = 0;
	virtual bool undo() = 0;
private:
	command_type ctype;
}

class command_factory{
	static command* generate_command(command_type _t){
		if ... else ...
	}
}

class helper{
public:
	void send_packet(packet p);
	packet recieve();
	void init(uint32 ip, uint16 port);
private:
	socket sock;
}

class context{
public:
	void synchronization();
	void get_state();
	void resolve(packet p);
private:
	obj instance;
}
{% endhighlight %}
Inside the code, the data is stored in the packet class, and the command is in command class. in a context, the instance is both maintained in local and remote side. Once the local call synchronization, the helper send packet p with command_type check, then the remote packet the data into a packet object and send it back with type as data. The local client recieve the packet and resolve it and synchronization the state. The instance object in the local context is the proxy of the remote obj, and the helper class helps to synchronizing their states.