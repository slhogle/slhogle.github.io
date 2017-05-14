---
layout:     post
title:      Installing Prokka in home folder on a compute cluster
date:       2017-01-19
summary:    Installing Prokka and (all) its dependencies without root privledges in your home environment.
---
### INTRODUCTION
Some colleagues have been asking about installing and using the fabulous [Prokka software](https://github.com/tseemann/prokka) on a compute cluster. Without root privledges this can be somewhat tricky if you don't know where to start. Particularly, on our compute cluster the default Perl version is old, and you don't have the option to install custom modules due to permissions restrictions. Here's how you get a working standalone Perl environment that you can update and modify freely and how you install the Prokka perl module dependencies. We'll be using the glorious [Perlbrew](https://perlbrew.pl/).

#### Setting up PERL5 environment
{% highlight bash %}
cd # change to your home directory
wget -O - https://install.perlbrew.pl | bash
perlbrew install 5.24.0
cpan App::cpanminus
{% endhighlight %}

#### Installing Prokka dependencies
These are the required perl modules for Prokka
{% highlight bash %}
cpanm Time::Piece XML::Simple Bio::Perl Digest::MD5
{% endhighlight %}

If you are interested in prediciting rRNAs then I would recommend installing the barrnap program
{% highlight bash %}
wget https://github.com/tseemann/barrnap/archive/0.7.tar.gz
tar zxvf barrnap-0.7.tar.gz
{% endhighlight %}

Finally, add barrnap-0.7/bin to your PATH

#### Installing Prokka itself
{% highlight bash %}
git clone https://github.com/tseemann/prokka.git # clone the prokka git repository into current directory
{% endhighlight %}

Add prokka-1.1X/bin to your PATH

{% highlight bash %}
prokka --setupdb
{% endhighlight %}

Good to go!