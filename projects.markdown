---
layout: page
title: Projects
permalink: /projects/
images:
  - image_path: /assets/images/projects_logos/autobrightness.png
    title: Auto-brightness
    web: /projects/autobrightness.html
  - image_path: /assets/images/projects_logos/RASP.png
    title: RASP
    web: /projects/RASP-ML.html
  - image_path: /assets/images/projects_logos/facetrack.png
    title: Face Track
    web: /projects/facetrack.html
---

<div class='clearfix'></div>
<div class='center' style = "margin-top:3%; margin-bottom:5%">
   <ul class="press">
      {% for image in page.images %}
      <li>
         <div class="press-logo">
         <a href='{{ image.web}}'>
         <img class="img_logo" src="{{ image.image_path }}"/>
         </a>
         </div>
         <span class="sr-only">{{ image.title}}</span>
      </li>
      {% endfor %}
   </ul>
</div>
