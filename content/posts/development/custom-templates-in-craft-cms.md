---
title: "Custom(ish) Templates In Craft Cms"
description: "I love Craft CMS 2.x and Structured Sections are Awesome, but I needed a little more flexibility."
date: 2017-07-15
draft: false
tags: [ "Craft CMS"]
---
## Custom Templates
I'm at the point with Craft CMS that I want to go back and re-do at least 15 previous websites. Once the information architecture is decided upon and the templates are created, the management, support and administration of a Craft-based site is a joy.

Recently, I worked with a client to re-do their WordPress site. We had about 85% of the information architecture nailed down prior to development, but as more content was finalized we discovered that we'd coded ourselves into a corner. Our nice and neat Structure sections - specifically the part where we have to choose the entry template - was going to cause us a few problems.

{{< figure src="/images/structured-entries.png" title="Default template for a Structure section" >}}

I wanted to keep the basics of the Structure channel intact, but I didn't want to add a whole nest of If/Then conditionals to the _entry template. After a little digging on Stack Overflow I found a few different people dealing with the same situation. I decided to solve mine like this:

```
{% block main %}

    {% include ['_custom/' ~ entry.uri, 'neo-master/_generic'] %}

{% endblock %}
```
This code is taken from my neo-master/_entry.twig template. It's the entry point for all the Structured entries within the site. The include has a simple conditional:

- does the entry.uri match an existing template in the _custom folder?
- if yes, include that file
- if no, continue to our generic template

For anyone wondering I'm using the absolutely brilliant [Neo plugin](https://github.com/benjamminf/craft-neo) for Craft CMS and decided to name the folder neo-master. There's no special meaning behind it - just a naming choice I made.

With this conditional in place, I can create any number of unique templates and place them in my _custom folder on the server. As long as the name of the template matches the URI of the entry this will work flawlessly.

Here's a screenshot of my templates. You can see that within the _custom folder I've created a few custom templates specifically for the about-us section.
{{< figure src="/images/template-list.png" title="" >}}

To see these live you can take a look at a couple examples:

- [Our Customers](https://www.exari.com/about-us/our-customers)
- [Executive Team](https://www.exari.com/about-us/executive-team)
- [Contact Us](https://www.exari.com/about-us/contact-us)


