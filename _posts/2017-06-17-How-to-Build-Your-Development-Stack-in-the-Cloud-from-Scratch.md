---
title: How to Build Your Development Stack in the Cloud from Scratch
layout: post
comments: true
date: 2017-06-16 01:00:00 +GMT
author: Sudeep Agarwal
categories: ['cloud-computing']
image: 'http://sudeep.co/images/post_images/2017-06-17-How-to-Build-Your-Development-Stack-in-the-Cloud-from-Scratch/stack.png'
---

<div align='center'>
<img width="700px" src="/images/post_images/2017-06-17-How-to-Build-Your-Development-Stack-in-the-Cloud-from-Scratch/stack.png">
</div>
<br>

Over the past couple of weeks, a number of friends approached me to ask how I had set up my website. I told them about I'd created the site using Jekyll and hosted it on Github Pages, but I had to host my web apps on Heroku because Github Pages only serves static content, and I also needed a database so I had to connect the web apps to Firebase...

And it struck me how inefficient my development stack was. I started wondering if I could do any better, and so this post is the result of completely recreating this stack from scratch in the cloud. There are a lot of great tutorials out there for each of the topics I'll be discussing, so this post does not intend to replicate any of those but instead provides an insightful collection of resources to hopefully give an idea of what a cloud computing setup entails.

<!--more-->

### Spoilt for Choice
In today's open-source landscape, there are an unimaginable number of libraries and frameworks for each task. Want to create a website with minimal effort? Use a drag-and-drop site creator like [Wix](https://www.wix.com/), [Weebly](https://www.weebly.com/), or [Wordpress](https://wordpress.com/), or maybe purchase some hosting on [GoDaddy](https://www.godaddy.com/) or [Namecheap](https://www.namecheap.com/) and let them do all the work. What about a web application? Use one of the million frameworks like [Meteor](https://www.meteor.com/), [Flask](http://flask.pocoo.org/) or [Django](https://www.djangoproject.com/). Then go ahead and host it on something like [Heroku](https://www.heroku.com/) or [PythonAnywhere](https://www.pythonanywhere.com/). Need a backend for the app? Connect it to [Firebase](https://firebase.google.com/). 

Building and hosting something simple on the web today has become at once simple and complex. Not only is it time consuming to explore all of these different services - creating accounts, setting them up, maintaing the services, it is also **expensive**. Hosting on Namecheap can cost nearly *$40 a year*, a backend on Firebase *$25 a month*, and application hosting on Heroku anything from *$25 to $500 a month per instance*. If you're a hobbyist developer or a student trying to create an online presence like me, it just doesn't make any sense to pay that much.

### Built for the Cloud

A wise programmer once said, "There is no cloud; it's just someone else's computer." 

We live in the age of cloud computing - every like, every tweet, every click. So why shouldn't the applications we build? Cloud computing is [driving down](https://www.forbes.com/sites/reuvencohen/2012/08/03/is-cloud-computing-really-cheaper/#7ce0c7427d44) development costs through economies of scale. In fact, major providers of cloud services such as [Amazon AWS](https://aws.amazon.com/) and [Google Cloud Platform](https://cloud.google.com/) are getting into bidding wars to remain competitive, which means lower prices for you and me. And the best part? The flexibility. Most cloud providers employ a pay-what-you-use business model, meaning that your business drives your cost.

Cloud computing is also *instant*. With a click of a button, you can scale your application across the world, adding cloud instances in New York, Bangalore or Singapore. The modular nature of cloud computing has encouraged businesses, startups, and even hobbyist developers, to make the switch. Need more processing power? Upgrade your cloud instance to one with a higher performance. Need more space? Connect a storage volume to your instance. And if you don't need it anymore, simply turn it off.

I think that by now, I've conviced you why it makes so much sense to both move your development to the cloud, and also streamline it onto a single platform. For the remainder of this post, I hope to briefly describe the approach that I took to do this. This approach is by no means the *best*, and as mentioned, there are plenty of different tools and services to do the same thing - but I'll try to make the case for my stack.

### The Development Stack

#### 1. Infrastructure as a Service (IaaS)
This is the very core of your stack, the single platform on which everything is built. It's the *cloud*, i.e a server halfway across the world with an operating system installed on it (typically a Linux distribution like Ubuntu). There are plenty of different options available, like [Amazon AWS](https://aws.amazon.com/), [Google Cloud Platform (GCP)](https://cloud.google.com/) and [DigitalOcean](https://www.digitalocean.com/). AWS and GCP are extremely powerful, but only really necessary if you require any of their plethora of additional services like [Google Cloud Dataproc](https://cloud.google.com/dataproc/), which allows you to set up Spark and Hadoop for data processing, or [Amazon S3](https://aws.amazon.com/s3/?hp=tile&so-exp=below), which offers scalable storage in the cloud. DigitalOcean is one of the cheapest options, and has everything I need (along with a really nice user-friendly interface). 

**My Pick: DigitalOcean**

* **Cheap**<br>
I am paying $5 a month for an instance with 512MB memory, 1 core processor, 20GB SSD disk and 1TB transfer.

* **Quick**<br>
DigitalOcean claims that you can 'spin up a droplet and get root access to a compute instance in only 55 seconds' (it's true).

* **User Friendly**<br>
Their platform and UI is extremely user friendly, and they have great tutorials and documentation. You can pretty much find a tutorial for anything that you're stuck on.

**Get Started: [Initial Server Setup with Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04)**



#### 2. Domain Name Registrar
This is where you register your domain name - in my case, *sudeep.co*. Again, there are plenty of options but it really doesn't matter where you get your domain from. Just compare prices across registrars. I got mine with [Namecheap](https://www.namecheap.com/) simply because I had registered another domain with them before, so I was familiar with the interface. But using Namecheap made me realize how much I appreciated their support.

**My Pick: NameCheap**

* **Excellent Support**<br>
One of the best things about Namecheap is their live chat. If I'm ever stuck on a hosting or DNS issue, I can instantly chat with one of their representatives to get live support instead of search on StackOverflow for hours.

**Get Started: [Register a New Domain](https://www.namecheap.com/domains.aspx)**



#### 3. Web Server
Once you have a cloud instance and domain, you need a web server to load your content. Web servers control how users access the hosted files using HTTP, which is the protocol that your browser uses to view web pages. With a web server, you can point different web applications to different ports. This makes your cloud instance very powerful - the same web server can serve multiple domains, subdomains and various web applications, allowing you to consolidate all your development.

<div align='center'>
<img width="400px" src="/images/post_images/2017-06-17-How-to-Build-Your-Development-Stack-in-the-Cloud-from-Scratch/web-server.svg">
</div>

Two of the most common open source web servers are [Apache](https://httpd.apache.org/) and [Nginx](https://nginx.org/en/), and both are solid options. Apache has been around for a really long time, and has great documentation and support. However, it follows a one-server-does-all model, which can sometimes struggle as web pages become richer, causing it to gain a reputation of being bloated. Nginx, which follows an [event-driven architecture](https://www.nginx.com/blog/inside-nginx-how-we-designed-for-performance-scale/), was created to solve many of these problems. Naturally, I decided to go with Nginx.

**My Pick: Nginx**

* **Event-Driven Architecture**<br>
Nginx was designed to solve many of the problems that Apache web servers face. For a more technical discussion, check out [Apache vs Nginx: Practical Considerations](https://www.digitalocean.com/community/tutorials/apache-vs-nginx-practical-considerations).

* **Good Documentation for DigitalOcean**<br>
The support on the DigitalOcean community seems to be better for Nginx.

**Get Started: [How To Install Nginx on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-16-04)**



#### 4. Static Site Generator
If you need a simple web page for a portfolio or blog (similar to what you're looking at right now), a static site is the way to go. Static sites have been extremely popular recently, and for good reason. They're extremely lightweight and fast, and web servers are really good at delivering static sites quickly. Many static site generators use templating engines like [Jinja](http://jinja.pocoo.org/) to easily generate dynamic markup and source code. While there are various options for static site generators, [Jekyll](https://jekyllrb.com/) is by far the most popular.

**My Pick: Jekyll**

* **Great Community**<br>
There are plenty of example projects, tutorials and themes available for you to explore. It's very easy to find answers to any question on StackOverflow as well.

* **Quick and Easy**<br>
You can create a new site in 3 lines of code. Yup, that's not a typo.

**Get Started: [How to Set Up a Jekyll Development Site on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-jekyll-development-site-on-ubuntu-16-04)**


#### 5. Web Framework
Awesome, so you've got a website. But what about your web applications? Great news - you can host all of them right here on your cloud instance. There are many different frameworks depending on which language you're comfortable with, such as [Meteor](https://www.meteor.com/) if you prefer JavaScript, and [Flask](http://flask.pocoo.org/) if you like Python. I am more comfortable with coding in Python, so I use Flask for all my web applications.

**My Pick: Flask**

* **Python**<br>
It's in Python, which means you get access to all of Python's amazing libraries from numpy for matrix manipulation, to pandas and scikit-learn for data analysis.

* **Easy to Setup**<br>
You can get a 'Hello World' application running in 7 lines of code, and it is very easy to add functionality. Flask is also one of the youngest Python web frameworks, which means that it has learnt from its predescessors, resulting in a very user-friendly experience.

**Get Started: [The Flask Mega-Tutorial](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world)**


#### 6. WSGI Server
Just as we need web servers to serve our web pages, we need also need web servers to serve our web applications. A Web Server Gateway Interface (WSGI) is essentially a container in a seperate process that is used to persist Python web applications on a different port than the web server used for your web page. Typically, [uWSGI](http://uwsgi-docs.readthedocs.io/en/latest/) or [gunicorn](http://gunicorn.org/) is used to set this up. After trying both, gunicorn seemed easier to set up, but that could just be my personal experience.

**My Pick: gunicorn**

* **Easier to Set Up**<br>
This could just be my personal experience. I would recommend starting with gunicorn, and jumping to uWSGI only if that fails.

**Get Started: [How To Serve Flask Applications with Gunicorn and Nginx on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-serve-flask-applications-with-gunicorn-and-nginx-on-ubuntu-16-04)**


#### 7. Remote Development
Another thing that makes this entire set up so powerful is that you essentially have an entire machine at your fingertips, so you can continue to develop your applications on your cloud instance as you would on your own computer. [Jupyter Notebook](http://jupyter.org/) is a really powerful web app that allows you create documents with live code. It's hard to explain with words, but I assure you that if you try it, you won't go back to an IDE. The best part is that you can use it remotely so you can edit code for an application that lives on the cloud, in the browser on your computer.

**My Pick: Jupyter Notebook**

* **Interactive Coding**<br>
The notebook offers a choice of 40 different programming languages, and a bunch of different widgets to improve your coding experience. You can even add your documentation or notes along the way in Markdown.

* **Notebook Server**<br>
Run the notebook on your cloud instance, and you can pull it up in your own browser.

**Get Started: [Running a Notebook Server](http://jupyter-notebook.readthedocs.io/en/latest/public_server.html)**


#### 8. Code Repository
This is pretty self explanatory. [GitHub](https://github.com/) seems to be the obvious choice for code management and maintainence, but I added this section in to discuss the possibility of local code maintainence. If you wanted, you could completely remove your dependence on GitHub and manage your gits in the cloud with [GitLab](https://about.gitlab.com/). I've not had a chance to look into this yet, but it sounds like this prove to be especially useful if you are collaborating with others on your projects, and would like the code to live locally on your server.

**Get Started: [How To Install and Configure GitLab on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-gitlab-on-ubuntu-16-04)**

### A Learning Experience

This entire exercise has been fundamental in building my understanding of web development, as well as of other concepts such as DNS, proxies, ports, server configurations, and even UNIX commands. It is by no means an easy exercise - it can be frustrating at times when you have no idea what you're doing. But it is ultimately really fulfilling to know that you've essentially built your own web hosting at a fraction of the cost (and learnt so much along the way).

The possibilities beyond this are endless. Some of the next steps include:
* Implementing a database that I can connect to my web applications
* Serving multiple websites from the same cloud instance
* GitLab integration to localize my development in the cloud
* Continue adding more projects (web applications, data visualizations, etc)


