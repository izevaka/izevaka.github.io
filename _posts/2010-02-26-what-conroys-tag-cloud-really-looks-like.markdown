---
layout: post
title: What Conroy's tag cloud really looks like
tags:
- conroy
- streisand-effect
- web
status: publish
type: post
published: true
meta:
  jd_tweet_this: 'yes'
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  wp_jd_target: http://www.somethingorothersoft.com/2010/02/26/what-conroys-tag-cloud-really-looks-like/
  wp_jd_bitly: http://bit.ly/9Cvfzw
---
Here is a screenshot of what the now infamous Conroy's tag cloud would look like without the "censorship" attempt:

<a href="http://www.somethingorothersoft.com/wp-content/uploads/2010/02/tagcloud.png"><img src="http://www.somethingorothersoft.com/wp-content/uploads/2010/02/tagcloud.png" alt="" title="tagcloud" width="320" height="336" class="alignnone size-full wp-image-307" /></a>

It's certainly not hugely prominent and, frankly, I am disappointed about that. It would have been way funnier if it was the biggest tag in the cloud.

Here is the greesemonkey script for those that feel inclined to verify the screenshot. It's very quicly hacked together and has the values hardcoded (which the original does too):

**Update** I was going to editorialize on this a bit more but forgot to do so before publishing. The whole debacle is frankly ridioculous and ironically goes to show two things:

* If there is a capability to censor something, at some point it will be abused. In this case it was the censoring of the ministers site, but with a nation-wide ISP filtering scheme who is to say other unflattering pages will not suddely become "Refused Classification".
* When something is to be censored, it will be done in the most half-arsed naive way. The minister's tag cloud was generated from what I suspect a hard-coded array of terms. It would have been absolutely trivial to simply remove the words "ISP Filtering", instead of matching it, which exposed the whole thing.

<div>
[code lang="javascript"]
// ==UserScript==
// @name           TagCloudUnhack
// @namespace      CONROY
// @description    Shows how the tag clound wuld look like without hiding ISP Filtering
// @include        http://www.minister.dbcde.gov.au/
// ==/UserScript==


function unique(x) {   
  tmp = new Array(0);   
  for(i=0;i&lt;x.length;i++) {      
    if(!contains(tmp, x[i])) {         
      tmp.length+=1;         
      tmp[tmp.length-1]=x[i];      
    }   
  }   
  return tmp;
}

function contains(x, e) {   
  for(j=0;j&lt;x.length;j++) {
    if(x[j]==e) {
      return true; 
    }
  }  
  return false;
}

function getTagClass(z) {   
  var tagClass = &quot;smallestTag&quot;;   
  if(z==smallest) {   
    tagClass=&quot;smallestTag&quot;;   
  } else if(z==largest) {   
    tagClass=&quot;largestTag&quot;;    
  } else if(z &gt;= large) {    
    tagClass=&quot;largeTag&quot;;    
  } else if(z &lt;= large &amp;&amp; z &gt;= medium) {    
    tagClass=&quot;mediumTag&quot;;    
  } else if(z &lt;= medium &amp;&amp; z &gt;= smallest) {    
    tagClass=&quot;smallTag&quot;;   
  }   
  return tagClass;
}



var a = 'NBN; Broadband; National Broadband Network; ABC; Broadcasting; National Broadcasters; SBS; Digital Switchover; Broadcasting; Digital Television; Youth Advisory Group; ISP Filtering; Cyber-Safety; Internet; Budget; ISP Filtering; Cyber-Safety; Internet; E-Health; Mobile Services; Digital Economy; Telephone Services; ICT; E-Health; Innovation; Broadband; Cyber-Bullying; ISP Filtering; Cyber-Safety; Emergency Services; Mobile Services; Telephone Services; E-Health; Emergency Services; Digital Regions; NBN; Broadband; National Broadband Network; Internet; Australian Broadband Guarantee; Broadband; Digital Economy; Internet; Post; E-Security; Internet; Digital Switchover; ABC; Digital Television; ISP Filtering; Cyber-Safety; Internet; E-Health; Emergency Services; Digital Regions; NBN; National Broadband Network; Digital Switchover; Digital Television; Mobile Services; Telephone Services; ABC; National Broadcasters; SBS; Emergency Services; Spectrum; Digital Switchover; Digital Television; NBN; National Broadband Network; Telephone Services; Productivity; Innovation; NBN Co; NBN; Broadband; National Broadband Network; Broadband; National Broadband Network; NBN; Broadband; National Broadband Network; Internet; Telephone Services; Mobile Services; Telephone Services; E-Security; ISP Filtering; Cyber-Safety; Internet; ABC; Digital Television; NBN; Broadband; National Broadband Network; Mobile Services; Spectrum; Cyber-Bullying; Youth Advisory Group; Consultative Working Group; ISP Filtering; Cyber-Safety; Digital Switchover; ABC; Broadcasting; Digital Television; Digital Switchover; Digital Television; Innovation; ICT; Community Television; Community Radio; Digital Switchover; NICTA; Do Not Call Register; NBN; Broadband; National Broadband Network; Budget; National Broadcasters; Digital Television; SBS; ABC; Broadcasting; Budget; National Broadcasters; Digital Television; SBS; Innovation; ICT; NICTA; Digital Economy; Budget; Digital Switchover; ABC; Broadcasting; Digital Television; Emergency Services; Do Not Call Register; Budget; Telephone Services; Digital Divide; Community Television; Community Radio; Broadcasting; Budget; Broadcasting; Budget; Digital Television; NBN Co; NBN; Broadband; National Broadband Network; Budget; Smart Grid; Innovation; Broadband; National Broadband Network; Budget; Digital Regions; ABC; NBN; Broadband; National Broadband Network; Budget; NBN; Broadband; National Broadband Network; Mobile Services; Mobile Services; Budget; Telephone Services; Digital Switchover; Broadcasting; National Broadcasters; Digital Television; NBN; Broadband; National Broadband Network; Digital Switchover; Digital Television; E-Security; Internet; Broadband; E-Security; National Broadband Network; Internet; E-Security; Broadband; E-Security; Internet; Broadband; Digital Economy; National Broadband Network; NBN; Broadband; E-Security; Digital Economy; National Broadband Network; Internet; E-Health; Emergency Services; Digital Regions; NBN; Broadband; National Broadband Network; ICT; Mobile Services; Telephone Services; Post; Digital Divide; NBN; Broadband; National Broadband Network; Digital Divide; Digital Literacy; Internet; NBN Co; NBN; National Broadband Network; Cyber-Bullying; CyberSmart; Cybersmart.gov.au; Youth Advisory Group; Consultative Working Group; Cyber-Safety; Productivity; Innovation; NBN; Broadband; Digital Economy; National Broadband Network; NBN Co; NBN; Broadband; National Broadband Network; Internet; Digital Switchover; Anti-Siphoning; Broadcasting; National Broadcasters; Digital Television; Innovation; NBN; Broadband; Digital Economy; National Broadband Network; NBN Co; NBN; Broadband; National Broadband Network; NBN Co; NBN; Broadband; National Broadband Network; NBN Co; NBN; Broadband; National Broadband Network; ICT; Digital Economy; Internet; Emergency Services; Mobile Services; Telephone Services; NBN Co; NBN; National Broadband Network; NBN Co; NBN; National Broadband Network; Digital Switchover; ABC; E-Security; Cyber-Safety; Telephone Services; SBS; NBN Co; NBN; Broadband; National Broadband Network; Australian Broadband Guarantee; Broadband; National Broadband Network; Internet; National Broadband Network; E-Health; Innovation; Emergency Services; Digital Regions; NBN; Broadband; National Broadband Network; NBN Co; NBN; Broadband; National Broadband Network; Post; Digital Switchover; Broadcasting; National Broadcasters; Digital Television; Cyber-Bullying; CyberSmart; Cybersmart.gov.au; Youth Advisory Group; Consultative Working Group; ISP Filtering; Cyber-Safety; E-Health; Innovation; Digital Regions; Broadband; National Broadband Network; NBN Co; NBN; Broadband; National Broadband Network; Internet; Community Radio; Broadcasting; Cyber-Bullying; CyberSmart; Cybersmart.gov.au; Youth Advisory Group; Consultative Working Group; ISP Filtering; Cyber-Safety; Internet; Australian Broadband Guarantee; NBN; National Broadband Network; Telephone Services; Internet; NBN; National Broadband Network; Internet; ABC; Broadband; National Broadband Network; Digital Television; SBS; Digital Switchover; Digital Television; Digital Switchover; Digital Television; E-Health; NBN; Broadband; Digital Economy; National Broadband Network; Internet; Telephone Services; Post; ABC; National Broadcasters; SBS; Smart Grid; Smart Grid; Broadcasting; Community Television; Broadcasting; Spectrum; National Broadcasters; Digital Television; NBN; Broadband; National Broadband Network; NBN; Broadband; SBS; Media; ABC; SBS; Digital Switchover; Digital Television; Digital Switchover; Digital Television; NBN; Cyber-Safety; Media; NBN; ; NBN; National Broadband Network; NBN; ; Australian Broadband Guarantee; Online Content; Cyber-Safety; Post; NBN Co; NBN; Broadband; National Broadband Network; Post; NBN Co; NBN; National Broadband Network; NBN Co; NBN; National Broadband Network; Digital Switchover; ABC; Broadcasting; Digital Television; Digital Divide; Digital Switchover; Broadcasting; Digital Economy; Radio; Spectrum; Digital Television; Digital Regions; Broadband; Digital Economy; National Broadband Network; Digital Switchover; Media; Broadcasting; Digital Television; Digital Switchover; Media; ABC; Broadcasting; Digital Television; Online Content; Cyber-Safety; Internet; ; Cybersmart.gov.au; Digital Economy; Cyber-Safety; Internet; NBN; NBN; National Broadband Network; NBN Co; NBN; Broadband; National Broadband Network; Internet; Digital Switchover; Broadcasting; Digital Television; Community Radio; Broadcasting; Radio; ; Broadband; National Broadband Network; Internet; NBN; Broadband; National Broadband Network; Internet; Innovation; Digital Switchover; NBN Co; NBN; Broadband; Digital Economy; National Broadband Network; Mobile Services; Mobile Services; Mobile Services; Broadband; National Broadband Network; Telephone Services; Internet; NBN; Broadband; National Broadband Network; Mobile Services; Youth Advisory Group; Consultative Working Group; E-Security; ISP Filtering; Cyber-Safety; Internet; Mobile Services; Broadband; Telephone Services; Internet; E-Security; ISP Filtering; Cyber-Safety; Mobile Services; Telephone Services; Mobile Services; E-Security; Cyber-Safety; Telephone Services; Mobile Services; Broadband; Telephone Services; Internet; E-Security; Cyber-Safety; Internet; Australian Broadband Guarantee; NBN; Broadband; National Broadband Network; Internet; NBN; Broadband; National Broadband Network; Internet; Digital Switchover; Broadcasting; Digital Television; Broadband; National Broadband Network; Australian Broadband Guarantee; Broadband; National Broadband Network; Internet; Telephone Services; Digital Switchover; Broadcasting; Digital Television; NBN; Broadband; National Broadband Network; Broadband; National Broadband Network; Australian Broadband Guarantee; Broadband; National Broadband Network; Mobile Services; E-Health; Productivity; Emergency Services; Broadband; Mobile Services; E-Security; Cyber-Safety; Internet; Media; Broadcasting; Mobile Services; Telephone Services; Internet; Mobile Services; Broadband; Telephone Services; Internet; Innovation; Australian Broadband Guarantee; NBN; Broadband; National Broadband Network; Budget; Internet; Youth Advisory Group; Consultative Working Group; Budget; ISP Filtering; Cyber-Safety; Community Radio; Media; Broadcasting; Cyber-Bullying; Consultative Working Group; Cyber-Safety; Australian Broadband Guarantee; Broadband; National Broadband Network; Youth Advisory Group; Consultative Working Group; ISP Filtering; Cyber-Safety; Innovation; Broadband; National Broadband Network; Broadband; National Broadband Network; NBN; Broadband; National Broadband Network; NBN; Broadband; National Broadband Network; Media; Digital Switchover; Digital Television; NBN; Broadband; National Broadband Network; E-Security; Internet; E-Security; Digital Economy; Internet; Innovation; Convergence; E-Security; Digital Economy; Cyber-Safety; Internet; E-Security; Cyber-Safety; Internet; Mobile Services; Broadband; Telephone Services; Internet; NBN; Broadband; National Broadband Network; Broadband; Digital Economy; National Broadband Network; Australian Broadband Guarantee; Broadband; National Broadband Network; Internet; NBN; National Broadband Network; ICT; E-Security; Mobile Services; Telephone Services; Australian Broadband Guarantee; Broadband; National Broadband Network; Online Content; Youth Advisory Group; Mobile Services; ISP Filtering; Cyber-Safety; Internet; Digital Switchover; Digital Television; NBN; National Broadband Network; ISP Filtering; Cyber-Safety; Telephone Services; Internet; National Broadband Network; Mobile Services; Telephone Services; Telephone Services; Emergency Services; Do Not Call Register; Telephone Services; Broadband; National Broadband Network; Australian Broadband Guarantee; Do Not Call Register; NBN; E-Security; National Broadband Network; Internet; Australian Broadband Guarantee; Do Not Call Register; E-Security; National Broadband Network; Media; ABC; Internet; Productivity; Digital Economy; Internet; Innovation; Youth Advisory Group; ISP Filtering; Cyber-Safety; Internet; Digital Television; Digital Switchover; Broadcasting; Digital Television; Community Radio; Broadcasting; Budget; Digital Switchover; Digital Television; Australian Broadband Guarantee; Mobile Services; Broadband; Telephone Services; Internet; Australian Broadband Guarantee; Do Not Call Register; Broadband; E-Security; National Broadband Network; ABC; National Broadcasters; SBS; Digital Switchover; Broadcasting; Digital Television; E-Health; Innovation; Emergency Services; Broadband; Digital Economy; Post; Australian Broadband Guarantee; Do Not Call Register; NBN; Broadband; E-Security; National Broadband Network; Mobile Services; Telephone Services; Digital Economy; Mobile Services; Telephone Services; Telephone Services; NBN; Digital Economy; National Broadband Network; E-Health; Broadband; National Broadband Network; Mobile Services; Telephone Services; Digital Switchover; Broadcasting; Spectrum; Digital Television; Digital Switchover; Spectrum; Digital Television; Youth Advisory Group; Cyber-Safety; Internet; Innovation; Digital Economy; Digital Economy; Australian Broadband Guarantee; Broadband; National Broadband Network; Youth Advisory Group; ISP Filtering; Cyber-Safety; Broadband; Digital Economy; Digital Switchover; ABC; Broadcasting; Digital Television; ICT; Broadband; E-Security; Digital Economy; NBN; Broadband; National Broadband Network; Productivity; Digital Economy; Mobile Services; Telephone Services; E-Security; ISP Filtering; Cyber-Safety; ';

var split = new Array();
split = a.split('; '); //string to array
var unique = unique(split);
/*unique.sort();*/ //sort alphabetically

var frequency = new Array();
var counts = new Array();
var largest = 0;
var smallest = 1;

/* Script creates an array and finds the most used tag */
for(var i=0; i&lt;unique.length; i++) {
   var mullet=0;
   unique[i] = unique[i].replace(/^\s+|\s+$/g, '') ;
   for(var j=0; j&lt;split.length; j++) {
      if (unique[i]==split[j]) {
          mullet=mullet+1;
      }
      frequency[i] = mullet;
   }
}

for(var d=0;d&lt;frequency.length;d++){
   largest=Math.max(largest,frequency[d]); //find largest
}
var diff = largest-smallest; //difference, smallest is always 1
var dist = diff/6; //distribution
var large = 1 + (dist*2);
var medium = 1 + dist;

// write out the tag cloud
if(unique.length != 0) {
//document.write('&lt;div id=\&quot;cloud-wrap\&quot;&gt;');
var out = &quot;&quot;;


//for(var i=0; i&lt;unique.length; i++)
for(var i=0; i&lt;=15/*&lt;-Important! increase this value by 1 everytime a keyword is excluded below*/; i++) 
{
	var z=0;
	for(var j=0; j&lt;split.length; j++) {
		if (unique[i]==split[j]) {
			z=z+1;
		}
		counts[i] = z;
	 }
	 var size = getTagClass(z);
	 //Customise the tag-cloud to display what shows up
	 //if (unique[i] == &quot;ISP Filtering&quot;)
	 //{
	//	 continue;
	// }
	 out += ('&lt;a class=&quot;'+size+'&quot; href=\&quot;http://www.minister.dbcde.gov.au/search?q='+unique[i]+'&quot;&gt;'+unique[i]+'&lt;/a&gt; ');
  }
}

var tagcloud = document.getElementById('tag-cloud');
tagcloud.innerHTML=out;

[/code]
</div>

