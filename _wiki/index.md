---
layout  : wikiindex
title   : wiki
toc     : true
public  : true
comment : false
regenerate: true
---

## [[programming]]

* [[kotlin-dsl]]
* [[kafka]]
* [[kotlin-script]]
* [[jackson-parameterized-type-cast]]
* [[gin-gonic]]
* [[postgres]]
* [[ANTLR]]
* [[Generics-covariance]]

## [[productivity]]

* [[vim]]
* [[shell]]

## [[computer-graphics]]

* [[Coordinate-Transformation]]

## [[machine-learning]]

* [[logistic-regression]]

## [[linux]]

* [[ubuntu-update-alternatives]]

---

## blog posts
<div>
    <ul>
{% for post in site.posts %}
    {% if post.public != false %}
        <li>
            <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">
                {{ post.title }}
            </a>
        </li>
    {% endif %}
{% endfor %}
    </ul>
</div>

