<!--https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet-->

# Welcome to vimontgames

Hello I'm Benoit. I am a graphics engineer and this is my page for personal projects.

![Book logo](docs/assets/images/vimontgames.gif)

# vgframework
My toy renderer/engine and my currently most active personal project. I created it in order to play with bindless rendering and raytracing.
You can check it here : [https://github.com/vimontgames/vgframework](https://github.com/vimontgames/vgframework)

  {% for post in site.posts %}
  <article>
    <h2>
      <a href="{{ post.url }}">
        {{ post.title }}
      </a>
    </h2>
    <time datetime="{{ post.date | date: "%Y-%m-%d" }}">{{ post.date | date_to_long_string }}</time>
    {{ post.content }}
  </article>
{% endfor %}
