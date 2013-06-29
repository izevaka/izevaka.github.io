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

[code lang="xml"]&lt;rootNode p1:xsi=&quot;http://www.w3.org/2001/XMLSchema-instance&quot; p1=&quot;http://www.foo.bar&quot; xmlns=&quot;http://www.foo.bar&quot;&gt;
&lt;/rootNode&gt;[/code]

Turns out the answer is pretty simple. I was correct in my assumption that in order to create a namespace alias one must simply add an attribute to the node. However the `xmlns` namespace is special and one cannot use the string `xmlns` or use the default namespace. Instead one must use `XNamespace.Xmlns` - like so: `new XAttribute(XNamespace.Xmlns + "xsi", "http://www.w3.org/2001/XMLSchema-instance")`. Here is complete code:

[code lang="csharp"]XNamespace xsi = XNamespace.Get(&quot;http://www.w3.org/2001/XMLSchema-instance&quot;);
            XNamespace defaultNamespace = XNamespace.Get(&quot;http://www.foo.bar&quot;);
            XElement root = new XElement(defaultNamespace + &quot;rootNode&quot;,
                new XAttribute(XNamespace.Xmlns + &quot;xsi&quot;, xsi.NamespaceName),
                new XAttribute(xsi + &quot;noNamespaceSchemaLocation&quot;, @&quot;..\path\to\schema.xsd&quot;));
[/code]
The above code will generate this:
[code lang="xml"]&lt;rootNode xmlns:xsi=&quot;http://www.w3.org/2001/XMLSchema-instance&quot; xsi:noNamespaceSchemaLocation=&quot;..\path\to\schema.xsd&quot; xmlns=&quot;http://www.foo.bar&quot; &gt;
&lt;/rootNode&gt;[/code]
