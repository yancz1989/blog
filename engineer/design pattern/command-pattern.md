---
layout: post
title: "Command Pattern"
description: ""
category: ['Engineer']
tags: [design pattern, software engineer]
date: 2016-02-15T22:15:10+08:00
---

I have always wonder how many program implement the method of redo and undo operations like the editor I use or some CAD software. After I learned the stack data structure, I know that I can use a stack to store the operation states, but still wondering how it is implemented. In the view of a C programmer, I don't quite understand how one can achieve such feature with so many different operation type. The command pattern solve the puzzle. Each opertion, like type in new charactor or change the format of the text in the document, can be regarded as a command. Command can be the parent class. Some command are quite simple, like typing, with only add a charactor to the string. But some command can be quite complicated like replace all certain words to another words. To execute each command, there are different procedure. Using polymorphism, overloading and inheritance, one can implement the **execute, set, undo** interfaces. A common Command Pattern parent class have the following structure:

{% highlight cpp linenos %}
class command{
public:
	virtual void execute() = 0;
	virtual void undo() = 0;
}
{% endhighlight %}

With this basic parent class, one can implement type_command and replace_command like:
{% highlight cpp linenos %}
class type_command : public command{
public:
	void set_char(char in){
		last = in;
	}
	void execute(){
		context.append(last);
	}
	void undo(){
		context.remove_last();
	}
private:
	char last;
}

class replace_command : public command{
public:
	void execute(){
		int i = 0;
		for(vector<string>::iterator word = words.begin();
			word != words.end(); word++){
			if(*word == src){
				*word = rpl;
				rpl_idx.push(i);
			}
			++i;
		}
	}
	void undo(){
		int i = 0;
		for(list<int>::iterator id = rpl_idx.begin();
			id != rpl_idx.end(); ++id){
			*id = src;
		}
	}

	void set(string _src, string _rpl){
		src = _src;
		rpl = _rpl;
	}
private:
	string src, rpl;
	list<int> rpl_idx;
}
{% endhighlight %}

Inside the code, use a stack<command*> as a context storage, your redo and undo operation is just a pointer moving upwards and downwards. The command pattern helps the engineer free from encapsulate different operations with same interface.