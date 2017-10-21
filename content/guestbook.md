---
title: "Guestbook - 留言簿"
slug: "guestbook"
---

You can leave me a message here.

<section id="comments">
  <div id="disqus_thread"></div>
  <script src="/js/disqusloader.min.js"></script>
  <script>
  var disqus_config = function () {
  
    this.page.url = "http:\/\/yihui.name" + location.pathname;
  
  };
  (function() {
    var inIFrame = function() {
      var iframe = true;
      try { iframe = window.self !== window.top; } catch (e) {}
      return iframe;
    };
    if (inIFrame()) return;
    var disqus_js = '//suredream.disqus.com/embed.js';
    
    if (location.hash.match(/^#comment/)) {
      var d = document, s = d.createElement('script');
      s.src = disqus_js; s.async = true;
      s.setAttribute('data-timestamp', +new Date());
      (d.head || d.body).appendChild(s);
    } else {
      disqusLoader('#disqus_thread', {
        scriptUrl: disqus_js, laziness: 0, disqusConfig: disqus_config
      });
    }
  })();
  </script>
  <noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
</section>