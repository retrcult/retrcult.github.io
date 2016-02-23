---
layout: page
title: Tags
permalink: /tags/
---

<style>
  .tag-posts {display: none;}
</style>

<!-- h4 class="pageTitle" id="tagTitle">Querying...</h4 -->

{% capture tags %}
    {% for tag in site.tags %}
        {{ tag[0] }}
    {% endfor %}
{% endcapture %}
{% assign sortedtags = tags | split:' ' | sort %}

{% for tag in sortedtags %}
<div id="tag-{{ tag }}" class="tag-posts">
  {% for post in site.posts %}
    {% for otag in post.tags %}
      {% if tag == otag %}
        <h4><a href="{{ site.url }}{{ post.url }}">{{ post.title }}</a></h4>
      {% endif %}
    {% endfor %}
  {% endfor %}
</div>
{% endfor %}

<script type="text/javascript">
    function getParameterByName(name) {
        var regex = new RegExp("[\\?&]" + name + "=([^&#]*)"),
            results = regex.exec(location.search);
        return results == null ? "" : decodeURIComponent(results[1].replace(/\+/g, " "));
    }

    window.onload = function() {
        var tag = getParameterByName('tag');
        if (tag && document.getElementById('tag-' + tag)) {
            document.getElementById('tag-' + tag).style.display = 'block';
            document.getElementById('pageTitle').innerHTML = 'Tag: ' + tag;
        } else {
            document.getElementById('pageTitle').innerHTML = 'Illegal Tag Query';
        }
    };
</script>
