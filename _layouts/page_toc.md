---
layout: default
---
<article class="post">

  <header class="post-header">
    <h1 class="post-title">{{ page.title | escape }}</h1>
  </header>

  <div class="toc" markdown="1">
* Table of contents
{:toc}
  </div>

  <div class="post-content">
    {{ content }}
  </div>

</article>