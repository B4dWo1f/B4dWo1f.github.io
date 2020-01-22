---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---

<img height="300pt" src="https://pm1.narvii.com/6385/e886b9a20567949c5d83f76504b8718cccccf3bf_hq.jpg" style="float: right;">

Welcome to my personal site!

 Lorem ipsum dolor sit amet, consectetur adipiscing elit. Ut lacus ex, condimentum eget lacus sit amet, egestas condimentum ipsum. Ut at imperdiet enim. Quisque at dapibus sapien, a scelerisque lectus. In hac habitasse platea dictumst. Duis tristique fringilla tortor, et tempus magna consectetur quis. Praesent interdum cursus justo, a volutpat mauris ullamcorper sit amet. Suspendisse potenti. Integer purus tortor, mollis nec metus a, vestibulum convallis nibh. Maecenas justo ante, pellentesque quis pellentesque nec, elementum ac mi. Maecenas vitae mauris viverra, ullamcorper nisi quis, scelerisque urna. Mauris nec ipsum lacinia, semper ante id, accumsan tortor. In faucibus magna dignissim mauris pulvinar, et tristique dui finibus. Cras et leo dignissim, rutrum leo ac, cursus metus. Cras mattis sem non imperdiet feugiat. Nam id scelerisque elit, in viverra libero. Fusce cursus fermentum ipsum, et tincidunt dui pharetra non.

Quisque aliquam, diam quis fermentum aliquet, mi orci ultrices mauris, ut tempus ipsum justo et felis. Etiam libero eros, placerat eget augue et, accumsan consectetur turpis. Phasellus at ex quis nunc euismod luctus. Donec sollicitudin neque nec ligula tempor consequat. Praesent vestibulum eleifend lorem a tristique. Suspendisse potenti. Donec in porttitor arcu. Nullam nec bibendum lacus. Integer tincidunt tellus ac neque sodales molestie. Praesent maximus est et justo viverra, a ultrices lectus pulvinar. Morbi interdum turpis non sem egestas, mattis fringilla magna tempus. Donec sed euismod felis. Duis semper suscipit nulla, et varius tortor cursus quis. Etiam facilisis iaculis nulla, pulvinar vehicula turpis.

{% for collection in site.collections %}
{% if collection.label != 'posts' %}
   <h2>
   <a class='title_link' href="/projects/">My {{ collection.label | capitalize }}</a>
   </h2>
   <ul>
     {% for item in site[collection.label] %}
       <li><a href="{{ item.url }}">{{ item.title }}</a></li>
     {% endfor %}
   </ul>
{% endif %}
{% endfor %}
