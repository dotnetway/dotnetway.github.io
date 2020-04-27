---
title:  "Compression error in IIS response."
date:   2018-02-11 15:25:31 +0100
categories:
  - WEB
tags:
  - Orchad
---

I've been seeing the following error for a few days in the answers given to me by the site I have published with Orchad.

![Compression error in IIS response](/img/Error_compresion_IIS.jpeg)

The error is completely random in terms of frequency and in the pages where it is given, so it has been a bit difficult to find out what really happened. As you can see it seems that the response of the IIS is returning a series of random characters.

The first investigation I made is that it only happened when the Orchad cache was enabled, when I disabled it it seems that the error in the response was not reproduced.

As a first solution to get out of the way it could be useful to disable the cache, but it is not viable, it is a viable solution in the long term or in places that have a lot of traffic, so by continuing to investigate find out what was happening.

It seems that the response that the IIS returns was being compressed with gZip twice. The reason seems to be that the Orchad OutputCache module is caching the response that had already been compressed with gZip and when the IIS serves that cached page it compresses it again with gZip again.

We can check if this is our case if refreshing the page that gives us the error reloads correctly and the error disappears.

It seems that some Hostings, including mine, modify the settings to make the compression before the cache. Then we have to disable it by adding the following directive in the section <system.webserver> of the web.config in the root of our site.

{% highlight xml %}
<system.webServer>
  <urlCompression doDynamicCompression="true"
                  doStaticCompression="true" 
                  dynamicCompressionBeforeCache="false" /> 
</system.webServer>
{% endhighlight %}

The dynamicCompressionBeforeCache attribute is set to false by default, so this bug only happens on servers where it has been changed.

If this does not work, which in my case has served, we would only have the option of setting the doDynamicCompression attribute to false, which would also disable compression of IIS responses. And as a last step we would only have to disable the OutputCache module of Orchad so that our site will not use cache. But as we have seen these solutions are not optimal but if it will not work otherwise it is the only thing we could do.

I hope it helps, because the truth has made me a little crazy.
