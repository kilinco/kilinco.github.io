---
layout: page
title: Book List
permalink: /books/
---

<table class="table table-condensed table-bordered">
  {% for row in site.data.Books %}
    {% if forloop.first %}
    <tr>
      {% for pair in row %}
        <th>{{ pair[0] }}</th>
      {% endfor %}
    </tr>
    {% endif %}

    {% tablerow pair in row %}
      {{ pair[1] }}
    {% endtablerow %}
  {% endfor %}
</table>