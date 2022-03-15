---
layout: post
title:  "Jekyll & Pi"
number: 1
date:   2022-03-15 08:15
excerpt: "In this article, we will setup a static Jekyll website and create a simple CD pipeline for it."
category: jekyll 
---
# Creating a static blog and documentation site with Jekyll

## What?

We are going to deploy a website with the help of Jekyll and a RaspberryPi 4. Afterwards we are going to setup a CD Pipeline to avoid manually deploying changes to our site every time we make them. So, what exactly is Jekyll?

From their official website: 
> Jekyll is a static site generator. It takes text written in your favorite markup language and uses layouts to create a static website. You can tweak the site’s look and feel, URLs, the data displayed on the page, and more.

> One of the great things about Jekyll is there’s no database. All content and site structure are files that a Git repository can version. Using a repository is optional but is recommended.

It is also the foundation for [GitHub Pages](https://pages.github.com/). So why not just use GitHub Pages then, you may ask? Because I am too much of a Scrooge McDuck.

## Now seriously. Why?

Simply because I do have a domain available and a couple of other reasons why I would like to share my journey, for example:

- Learning
- Interactions
- Motivation
- Input
- I am a loner with no hobbies (jk, I love my girlfriend and her patience with me while I am sitting in front of my desk doing pointless stuff like this all day long)

## Our setup

I will try to use my Raspi 4 with 4 Gigs of RAM for this because I do have it idling around for months now and this way it will finally have a purpose. This Pi already has the [Oh My Zsh Shell](https://ohmyz.sh/) with theme and plugins installed and configured (I will probably write some documentation for my shell setup here at some point). Let's see how it goes.

## Alright, let's go

### Installing prerequisites

- Ruby version 2.5.0 or higher
- RubyGems
- GCC and Make

Nothing unusual happening here. First off we install ruby and some other prerequisites:

```bash
sudo apt-get -y install ruby-full build-essential zlib1g-dev
```

This spawned a popup asking me which services I want to restart. I kept the suggested services checked and confirmed hitting enter.

Next, we want to avoid installing RubyGems packages (called gems) as the root user. Instead, we set up a gem installation directory for our user account. The following commands will add environment variables to our ~/.zhrc file to configure the gem installation path:

```bash
echo '# Install Ruby Gems to ~/gems' >> ~/.zshrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.zshrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

> :warning: In my case I had to move the export commands close to the top of my .zshrc because other configurations in
> the file would conflict and result in errors such as:  
> `zsh: command not found: bundle (after gem install bundle)`


Finally, we install Jekyll and Bundler:

```bash
gem install jekyll bundler
```

This can take a while depending on your setup. The Pi I had lying around for months had to get used to his new life and took around 10 minutes to complete its job.

### Creating a new site

First off, we tell `jekyll` to create our new site at ~/blog (no, you don't have to create the folder first):

```bash
jekyll new ~/blog
```

I will call this our root folder from now on.
Next, we change into our new directory, build the site and make it available on our Pi server:

```bash
cd ~/blog && bundle exec jekyll serve
```

> :warning: **Ruby Version 3.0.0+**: As of 03/14/2022 the official documentation says this step [may fail](https://github.com/github/pages-gem/issues/752). You may fix it by adding `webrick` to your dependencies: `bundle add webrick`

This brings up our server and allows us to connect to 127.0.0.1:4000. Though because I was installing this on my Pi and wanted to connect from my desktop I had to add a few lines to the _config.yml in my ~/blog folder:

```yaml
# deployment
host: 0.0.0.0
port: 5000
```

Et voilà! Our site is up and running:

![](/assets/images/template.png)

Alright, cool. So far we installed Jekyll and created our first site. It's just the default template with a default post for now, but that will change soon.

## Customizing the site

### Creating our first post

Okay, we got the template site up and running, now it's time to fill it with our own content. This is pretty simple.
Jekyll looks for files with the proper format in the ~/blog/_posts folder. This file must contain what Jekyll calls the [YAML Front Matter](https://jekyllrb.com/docs/step-by-step/03-front-matter/).
A typical Front Matter section could look like this:

```yaml
---
layout: post
title:  "Creating a static documentation and blog site with Jekyll"
number: 1
date:   2022-03-15 08:15
excerpt: "In this article, we will setup a static Jekyll website and create a simple CD pipeline for it."
category: Tech
---
```
