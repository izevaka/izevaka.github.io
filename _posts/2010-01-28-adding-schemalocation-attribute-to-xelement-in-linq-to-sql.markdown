---
layout: post
title: Adding schemaLocation attribute to XElement in LINQ to SQL
tags:
- General
status: publish
type: post
published: true
meta:
  _jd_twitter: ''
  _syntaxhighlighter_encoded: '1'
  _edit_last: '1'
  _jd_wp_twitter: ''
  _jd_post_meta_fixed: 'true'
  _wp_jd_wp: ''
  _jd_tweet_this: 'yes'
  _wp_jd_clig: ''
  _wp_jd_bitly: http://bit.ly/9QI3g8
  _wp_jd_yourls: ''
  _wp_jd_url: ''
  _wp_jd_target: http://www.somethingorothersoft.com/2010/01/28/adding-schemalocation-attribute-to-xelement-in-linq-to-sql/
---
I have spent a bit of time trying to figure out how to generate an XML document with all the XML namespace paraphernalia - including schema location. I got stuck trying to create the `xmlns:xsi` attribute - LINQ to XML kept creating a namespace alias for me and calling it `p1` like so:

{% highlight xml %}<rootNode p1:xsi="http://www.w3.org/2001/XMLSchema-instance" p1="http://www.foo.bar" xmlns="http://www.foo.bar">
</rootNode>{% endhighlight %}

Turns out the answer is pretty simple. I was correct in my assumption that in order to create a namespace alias one must simply add an attribute to the node. However the `xmlns` namespace is special and one cannot use the string `xmlns` or use the default namespace. Instead one must use `XNamespace.Xmlns` - like so: `new XAttribute(XNamespace.Xmlns + "xsi", "http://www.w3.org/2001/XMLSchema-instance")`. Here is complete code:

{% highlight csharp %}XNamespace xsi = XNamespace.Get("http://www.w3.org/2001/XMLSchema-instance");
            XNamespace defaultNamespace = XNamespace.Get("http://www.foo.bar");
            XElement root = new XElement(defaultNamespace + "rootNode",
                new XAttribute(XNamespace.Xmlns + "xsi", xsi.NamespaceName),
                new XAttribute(xsi + "noNamespaceSchemaLocation", @"..\path\to\schema.xsd"));
{% endhighlight %}
The above code will generate this:
{% highlight xml %}<rootNode xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="..\path\to\schema.xsd" xmlns="http://www.foo.bar" >
</rootNode>{% endhighlight %}
