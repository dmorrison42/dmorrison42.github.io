## [About Me](/about)

## Pattern Writeups

These are here primarily as a reference guide for myself, but if you find this useful, or find any errors, please feel free to let me know on [GitHub](https://github.com/{{site.repository}})

{% for post in site.patterns %}

- [{{post.title}}]({{ post.url | prepend: site.baseurl }})

{% endfor %}

## Tools

- [RF Attenuator (Pad) Calculator](pad-calculator)

## Kayaking Boat Launches

{% for post in site.boat_launches %}

- [{{post.title}}]({{ post.url | prepend: site.baseurl }})

{% endfor %}
