---
layout: page.ejs
title: Modular content fields
category: jekyll
position: 7
---

<div class="note">
**Note** This guide assumes you have a basic knowledge of how Modular content fields work in DatoCMS. If this is not the case, please read [this introduction](/schema/modular-content.html) first.
</div>

Suppose a `blog_post` model has a modular-content field called `body`, which in turn accepts the following models as [building-blocks](/schema/modular-content.html):

* Model `blog_post_text_block`: made of a `text` field (*multi-paragraph text*);
* Model `blog_post_quote_block`: made of a `quote` field (*multi-paragraph text*) and `author` field (*single-line string*);
* Model `blog_post_gallery_block`: made of a `gallery` field (*image gallery*);

A modular-content field works much like a [*multiple links* field](/jekyll/links.html), as it returns an array of the inner records. In your `dato.config.rb` file you can dump a modular-content field inside the frontmatter of a post like this:

```
# inside a "_posts" directory...
directory "_posts" do

  # ...iterate over the "Blog post" records...
  dato.blog_posts.each do |article|

    # ...and create a markdown file for each article!
    create_post "#{article.slug}.md" do
      frontmatter(
        :yaml,
        layout: "article",
        title: article.title,
        author: article.author.name,
        body: article.body.to_hash
      )
    end
  end
end
```

Then, in your `src/_layouts/article.html`, you can present it this way:

```django
---
layout: default
---

<article>
  <header>
    <h1>{{ page.title }}</h1>
    <p>{{ page.author }}</p>
  </header>
  {% for record in page.body %}
    {% if record.item_type == "blog_post_text_block" %}
      <div>
        {{ record.text }}
      </div>
    {% elsif record.item_type == "blog_post_quote_block" %}
      <blockquote>
        {{ record.quote }}
        <footer>
          <cite>{{ record.author }}</cite>
        </footer>
      </blockquote>
    {% elsif record.item_type == "blog_post_gallery_block" %}
      <div class="gallery">
        {% for image in record.gallery %}
          <img src="{{ image.url }}" alt="{{ image.alt }}" title="{{ image.title }}" />
        {% endfor %}
      </div>
    {% endif %}
  {% endfor %}
</article>
```

<div class="note">
**Warning** In this example, we named the modular-content field `body`. This is intentional: do not name your modular-content field `content`, as in Jekyll this is a special attribute and you won't be able to read it in your layout!
</div>
