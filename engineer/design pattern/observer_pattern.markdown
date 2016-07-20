---
layout: post
title: Observer Pattern
modified:
categories: ['Engineer']
excerpt:
tags: [design pattern, software engineer]
image:
  feature:
date: 2016-01-09T10:46:55+08:00
---

From today, I will publish some of my readings to this blog, hoping to share and better remember what I have learnt. These essays contain subjects in software engineer, tools used in research, and others.
Design pattern is not new since last century since the development of Object Oriented Programming(OOP). I finished the Gang of Four very long time ago, but in machine learning research, these skill or engineer thought has gone away as I need not use any of them with daily programming in scripts such as MATLAB or Python. Recently, I start reading "Head First Design Pattern", which is easy to understand.
It has server principles for starter:

1. Encapsulate what varies, meaning you should encapsulate all in OOP, so that no roles is lost;
2. Favor composition over inheritance;
3. Programming to interfaces not implementations;
4. Strive for loosely coupled designs between objects that interacts.


The so called observer pattern defines a one-to-many dependency between objects so that when one object changes state, all of its dependents are notified and updated automatically. In detailed explained scene, using **subscriber/publisher** model, one object act as the publisher, which maintain a state, while other objects knowns as subscriber, want to know these states. If the code was coupling into the same segment, when adding new subscribers, besides write a new subscriber class and object, the publisher will need to be modified at the same time. By decompositing the code into subscriber with **update** interface, and publisher with **subscribe, notify, remove** interfaces, the code will be neat enough and easy to maintain. The subscriber/publisher have been implemented below.
{% highlight cpp linenos %}
#ifndef DESIGN_PATTERN_OBSERVER_H
#define DESIGN_PATTERN_OBSERVER_H

#include "../phantom.h"

namespace phantom{
namespace design_pattern{
namespace observer{

template<class DATA>
class subscriber{
public:
  virtual void update(const DATA& data) = 0;
  long long get_id() { return id; };
private:
  long long id;
  DATA state;
};

template<class DATA>
class publisher{
public:
  publisher(){ count = 0; }

  virtual void subscribe(subscriber<DATA>* sub){
    subs.insert(pair<long long, subscriber<DATA>*>(count++, sub));
  }

  virtual void notify_all(const DATA& ndata) {
    for(typename map<long long, subscriber<DATA>*>::iterator it = subs.begin();
        it != subs.end(); ++it)
      it->second->update(ndata);
  }
  virtual void notify(long long id, const DATA& ndata){
    subs.find(id)->second->update(ndata);
  }

  virtual void remove(subscriber<DATA>* sub){
    typename map<long long, subscriber<DATA>* >::iterator it = subs.find(sub->get_id());
    subs.erase(it);
  };
protected:
  map<long long, subscriber<DATA>*> subs;
  atomic<long long> count;
};

}
}
};

#endif
{% endhighlight %}
For example,  small agencies usually have no professional news team and will subscribe from large agencies with fees. When news happens, large agencies will collect news and notify all the subscribers. When new small agencies comes, only act as subscribers and listen to the large agencies. Implementation code is shown below. 

{% highlight cpp linenos %}
#include "../observer.h"

using namespace std;
using namespace phantom::design_pattern::observer;

struct news{
  char title[256];
  char content[1024];
};

class large_agency : public publisher<news>{
public:
  void notify(long long id, const news& data){
    subs.find(id)->second->update(data);
    printf("message sent to %lld\n", id);
  }
  void new_gen_distribute(){
    news nnews;
    strcpy(nnews.title, "Breaking News, War Start!\n");
    strcpy(nnews.content, "It's a joke, peace will stay the same.\n");
    notify_all(nnews);
  }
};


class small_agency : public subscriber<news> {
public:
  small_agency(const char* _name){ strcpy(name, _name); }
  void update(const news& data) {
    printf("%s recieved news %s, content : %s\n", name, data.title, data.content);
  }
private:
  char name[128];
};

int main(){
  large_agency xinhua_agency;
  small_agency A("Reuters"), B("United"), C("Globles");
  xinhua_agency.subscribe(&A);
  xinhua_agency.subscribe(&B);
  xinhua_agency.subscribe(&C);
  xinhua_agency.new_gen_distribute();
    return 0;
}
{% endhighlight %}

