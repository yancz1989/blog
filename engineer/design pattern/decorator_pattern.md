---
layout: post
title: "Decorator Pattern"
description: ""
category: ['Engineer']
tags: [design pattern, software engineer]
date: 2016-01-25T09:43:55+08:00
---

This is an essay on design pattern, still from reading "Head First Design Pattern". Decorator Pattern is for another important principle on OO designing: Classes should be open for extension but closed for modifications.

In example, consider situation in which we are build a system storing and serving the personal informations. In most cases, the designer of the system can not include all the meta inforamtion at the first time it is build. Once your bose need some new information you need a new member in the class **person**, then you modify the interfaces in **person** and the derived class like **manager**, **engineer** will need to be modified at the same time, and this would be a huge project as it will be an increasing class explosion.

Actually you can view different meta information as a description, without any preference order, and decorator pattern can help you solve the problem without introducing any modifications on the super class **person**. It works as follows: every decorator inherents from the same parent class in which the same interfaces are implemented for the same function. When add new decorator, just wrap old decorators inside the new decorator recurisively. The follow example shows how this work.

The super class **person**.
{% highlight cpp linenos %}
class person{
public:
	virtual void show(){
		printf("This person is %s, ", name.c_str());
	};
	person(string _name){
		name = _name;
	};
	person(){
		;		
	};
private:
	string name;
};
{% endhighlight %}
For decorator manager and engineer:
{% highlight cpp linenos %}
class manager_decorator : public person{
private:
	person* father;
public:
	manager_decorator(person* p){
		father = p;
	}
	virtual void show(){
		father->show();
		printf("a manager, ");
	}
};

class engineer_decorator : public person{
private:
	person *father;
public:
	engineer_decorator(person *p){
		father = p;
	}
	virtual void show(){
		father->show();
		printf("an engineer, ");
	}
};
{% endhighlight %}
For gender decorators:
{% highlight cpp linenos %}
class male_decorator : public person{
private:
	person *father;
public:
	male_decorator(person *p){
		father = p;
	}
	virtual void show(){
		father->show();
		printf("gender male.\n");
	}
};

class female_decorator : public person{
private:
	person *father;
public:
	female_decorator(person *p){
		father = p;
	}
	virtual void show(){
		father->show();
		printf("gender female.\n");
	}
};
{% endhighlight %}
And the main function describes how to use the decorator pattern. Here recurisively means that we can put description decorator one by one by initial the father pointer recurisively. Hence calling the last decorator **show()** will recurisively show the predecessor's information.
{% highlight cpp linenos %}
int main(){
	person p(string("Alice")), p2(string("Johnson"));
	manager_decorator m1(&p);
	engineer_decorator ed1(&p2);
	male_decorator md(&ed1);
	female_decorator fd(&m1);
	fd.show();
	md.show();
	return 0; 
}
{% endhighlight %}