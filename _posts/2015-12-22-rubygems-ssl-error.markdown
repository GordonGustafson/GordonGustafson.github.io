---
layout: post
title:  "Rubygems SSL 'certificate verify failed' on Debian Stretch"
date:   2015-12-22
tags: [how-i-fixed debian]
---

{% highlight sh %}
$ sudo gem install bundler
ERROR:  Could not find a valid gem 'bundler' (>= 0), here is why:
        Unable to download data from https://rubygems.org/ - SSL_connect returned=1 errno=0 state=error: certificate verify failed (https://api.rubygems.org/specs.4.8.gz)
$ gem which rubygems
/usr/lib/ruby/2.2.0/rubygems.rb
$ cd $(dirname $(gem which rubygems))/rubygems/ssl_certs
$ sudo curl --capath /usr/share/ca-certificates/mozilla/ -O https://raw.githubusercontent.com/bagder/ca-bundle/master/ca-bundle.crt
$ sudo mv ca-bundle.crt ca-bundle.pem  # file needs to have a '.pem' extension
$ cd -                                 # go back to where we ran `sudo gem install bundler`
$ sudo gem install bundler
Successfully installed bundler-1.11.2
Parsing documentation for bundler-1.11.2
Done installing documentation for bundler after 3 seconds
1 gem installed
$
{% endhighlight %}

The `curl --capath /usr/share/ca-certificates/mozilla/` is because [curl couldn't find any SSL certificates on my machine][curl-ssl-error].

We're trusting a **lot** of authorities here, but it's better than using HTTP.

[curl-ssl-error]: {% post_url 2015-12-22-curl-ssl-error %}
