<ul class="wikiMenu">
  {% for p in site.pages %}
    {% if p.menu == "wiki" %}
    <li><a class="post-link" href="{{ p.url | prepend: site.baseurl }}">{{ p.title }}</a></li>
    {% endif %}
  {% endfor %}
</ul>