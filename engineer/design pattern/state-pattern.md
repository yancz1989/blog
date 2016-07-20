---
layout: post
title: "State Pattern"
description: ""
category: ['Engineer']
tags: [design pattern, software engineer]
date: 2016-02-17T16:07:07+08:00
---

Hearing about the state machine, first I recall the Turing machine and finite state machine. Actually any time we want to describe some system with server different state, using state pattern is a good choice. Many property can be described as a state like the inner entities' state. In the book "Head First Design Pattern", the came up with the example of Gambling machine. It has 4 state,
has_quater, no_quater, gumball_sold, out_of_gumball. You need to keep a descriptor to determine the current state of machine. But if you naively implement the operation method, you will have to write many pairs of if-else. If the system has n state and m operations, you will have to implement mn if-else. And imagine if the requirement changes, add a new state for instance, what will happen? What a mess.

![Alt text](/assets/images/engineer/20160217_state_pattern/figure_1.png "图1")
The state pattern works in ways of encapsulating all the operations into the state class. In the example of gamble machine, the state class has all the interfaces to the operations.

{% highlight cpp linenos %}
class state{
public:
	void set_context(gamble_context* _context){
		context = _context;
	}

	virtual void insert_quarter() = 0;
	virtual void eject_quarter() = 0;
	virtual void turn_crank() = 0;
	virtual void dispense() = 0;
private:
	gamble_context *context;
}
{% endhighlight %}

The interfaces need to be implemented and each time on operation, the context object change its state. And the gamble_context class works below.

{% highlight cpp linenos %}
class gamble_context{
public:
	gamble_context(){
		sold_out_state_i = new sold_out_state(this);
		no_quarter_state_i = new no_quarter_state(this);
		has_quarter_state_i = new has_quarter_state(this);
		sold_state_i = new sold_state(this);
		cur = no_quarter_state_i;
	}

	void insertQuarter() {
		cur->insertQuarter();
	}

	void ejectQuarter() {
		cur->ejectQuarter();
	}

	void turnCrank() {
		cur->turnCrank();
		cur->dispense();
	}

	setState(state s) {
		this->cur = s;
	}

	state* get_sold_out_state(){
		return sold_out_state_i;
	}

	state* get_no_quarter_state(){
		return no_quarter_state_i;
	}

	state* get_has_quarter_state(){
		return has_quarter_state_i;
	}

	state* get_sold_state(){
		return sold_state_i;
	}
private:
	state *sold_out_state_i;
	state *no_quarter_state_i;
	state *has_quarter_state_i;
	state *sold_state_i;
	state* cur;
}
{% endhighlight %}

A sample state is implemented like the following. Every time if new state is added to the system, only modify the code of related state is enough. Or a new operation, modify the code of related state and gamble_context is enough.
{% highlight cpp linenos %}
class has_quarter_state: public state {
public:
	has_quarter_state(gamble_context* _context){
		context = _context
	}
	void insertQuarter() {
		printf("You can’t insert another quarter");
	}
	void ejectQuarter() {
		printf("Quarter returned");
		context->setState(context->get_no_quarter_state());
	}
	void turnCrank() {
		printf("You turned...");
		context->setState(context->get_sold_state());
	}
	void dispense() {
		printf("No gumball dispensed");
	}
}
{% endhighlight %}