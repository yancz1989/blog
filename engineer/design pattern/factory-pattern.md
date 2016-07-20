---
layout: post
title: Factory Pattern
modified:
categories: ['Engineer']
excerpt:
tags: ['design pattern', 'software engineer']
image:
  feature:
date: 2016-02-08T09:43:55+08:00
---

Factory pattern helps you create objects inherited from the same parent class. Usually you can substitute the factory method or class with group of if-else, which is messy and difficult to maintain. The principle I learned from factory pattern is we need to design OO from abstractions not depend on the concrete objects, also known as programming to interface not implementations. In OO grammar, you can use **polymorphism and overloading** to define an interface of create objects but let the subclass decide the right operation processing data flow. For example, you are ordered to write an automatical trading system for bond option trade, you know there are 3 kinds of bond, Euro option, American option, Bermuda option and all traded in London, New York, and Hong Kong security exchange. They have different trading rules and authentication yet you need to implement the interfaces. The bond option should have basic elements like start date, duration, interest rates, currency,  and price. If no design pattern is used, we could implement the order generation like:

{% highlight cpp linenos %}
enum option_type {
	American,
	European,
	Bermudan
}
class option{
public:
	long long date;
	int duration;
	short currency;
	bool isput;
	double price;
	double strike;
	double profit( double price){
		double ret = 0;
		if(strike + price < price_on_maturity && !isput)
			ret = price_on_maturity - price - strike;
		else if(strike > price + price_on_maturity && isput)
			ret = strike - price - price_on_maturity;
		return ret;
	}
	list<long long> dates;
}

double execute(double price_on_maturity, long long today, option_type type, option opt){
	double ret = -1;
	if(type == Bermudan){
		for(list<long long> iterator it = dates.begin(); it != dates.end(); ++it){
			if(*it == today)
				ret = profit(price_on_maturity);
		}
	} else if (type == American){
		if (today <= duation + date)
			ret = profit(price_on_maturity);
	} else if (type == European) {
		if(today == duation + date)
			ret = profit(price_on_maturity);
	}
	return ret;
}
{% endhighlight %}
Or you have to implment a exchange center class with interfaces below.
{% highlight cpp linenos %}
class exchange{
public:
	int exchange_no;
	void create_option( ... );
	void execute_option(option* opt);
	void record_option(option* opt);
	void warning_option(option* opt);
	...
}
{% endhighlight %}

The pair of if-else don't bother too much if the logic is just that easy. Only a redundant **list<long long>** is added to the class. You can then implement the execute code for each exchange. However, what if a new type of option is added into the exchange set. More if-else? There is only one interface to implement here, what if we have more than ten...

The task is easy to understand as we don't want to modify the code of existing class and implementations. The factory is the solution to achieve this goal by adding a new class. 

{% highlight cpp linenos %}
class option{
public:
	long long date;
	int duration;
	short currency;
	bool isput;
	double price;
	double strike;
	virtual double execute(long long today, double price) = 0;
	double profit( double price){
		double ret = 0;
		if(strike + price < price_on_maturity && !isput)
			ret = price_on_maturity - price - strike;
		else if(strike > price + price_on_maturity && isput)
			ret = strike - price - price_on_maturity;
		return ret;
	}
}

class American_option: public option{
public:
	double execute(long long today, double price_on_maturity){
		if(today <= duration + date)
			return profit(today, price_on_maturity);
		else
			return -1.0;
	}
}

class Euro_option: public option{
public:
	double execute(long long today, double price_on_maturity){
		if(today == duation +  date)
			return profit(today, price_on_maturity);
		else
			return -1.0;
	}
}

class Bermudan_option : public option{
public:
	double execute(long long today, double price_on_maturity){
		double ret = -1.0;
		for(list<long long>::iterator it = dates.begin(); it != dates.end(); ++it){
			if(*it == today){
				ret = profit(price_on_maturity);
				break;
			}
		}
		return ret;
	}
	void set_dates(list<long long> _dates){
		dates = _dates;
	}
private:
	list<long long> dates;
}

class option_factory{
public:
	void factory_method( option* popt, option_type type, long long date, int duration,
		short currency, bool isput, double price, double strike,
		list<long long> dates = list<long long>()){
		if(type == American)
			popt = new American_option();
		else if(type == European)
			popt = new Euro_option();
		else if(type == Bermudan){
			popt = new Bermudan_option();
			popt->dates = dates;
		}
		popt->date = date;
		popt->duration = duation;
		popt->currency = currency;
		popt->isput = isput;
		popt->price = price;
		popt->strike = strike;
	}
}
{% endhighlight %}

One may think the factory pattern have just move the pairs of if-else to the factory method. The most important advantages of factory pattern are below:

1. It helps you easily add new subclasses like Asia_option without need to modify the code in other classes such as the **exchange** class;
2. The factory method can be a parent class and help you create more factory subclass to apply different creation logic;
3. It encapsulate all the operation of the same abstraction to the same interface.

The factory pattern is used if you have the problem to create different objects but have same procedures to call after creation. It define the **interface** not the implementations.
