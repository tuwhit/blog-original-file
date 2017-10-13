---
title: 블로그 개설
date: 2017-09-24 21:00:45
photos: 
- /images/BEUP3482.jpg
tags:
- blog
- github
- hexo
comments: true
---
-----------------------------
[Github Pages](https://pages.github.com/), [Hexo](https://hexo.io/) 로 블로그 개설! [Jekyll](https://jekyllrb-ko.github.io/)을 이용해서 만들까 하다가 Ruby는 회사에서 많이 쓰니 Node.js 기반인 Hexo로 선택했다. 근데 뭐 Node.js 까막눈이어도 눈칫껏 만져보니 기존 테마에 살짝 커스텀하는 정도는 금방 하는듯. Markdown 문법도 잘 몰라서 [Markdown 작성법](https://gist.github.com/ihoneymon/652be052a0727ad59601) 보면서 이래저래 써보는 중인데 꽤 재밌다. 블로그 메뉴를 어떻게 나눌지, footer 엔 어떤 위젯을 넣을지, 로고는 뭘로 할지 고민. 10대때 열심히 네이버 블로그 꾸미던 시절 생각난다. 하악하악 재밌어!

[Daily 테마 위키](https://github.com/GallenHu/hexo-theme-Daily/wiki)에 comment field 추가하려면 `_config.yml` 파일에
`disqus_shortname: your-disqus-shortname` 만 추가해주면 된다고 했는데 코멘트 영역이 안뜸...^ㅅㅠ 다행히 [disqus 홈페이지](https://disqus.com/)에 들어가보니 site - installation 메뉴에 코드가 제공돼있었다.
``` javascript
<div id="disqus_thread"></div>
<script>

/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
/*
var disqus_config = function () {
this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');
s.src = 'https://tuwhit.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
                            
```
Disqus가 들어갈 부분에 위 코드를 넣으니 해결!

테마 적용후 deploy 했는데 반영되는데 시간이 꽤 걸리나보다. 계속 깨져보이길래 제대로 deploy 안된줄알고 구글링중이었는데, 몇분 지나니까 잘 반영돼 있음.
