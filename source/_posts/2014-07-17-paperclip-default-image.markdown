---
layout: post
title: "Paperclip Default Image"
date: 2014-07-17 21:07

---
Paperclip makes managing images really easy, but I dont always want to upload a image to see how it will be laid out on a page, or use conditional statements to safely rescue from a missing image.
With Paperclip we can define a `:default_url` that gets returned if the attachments url is missing.
I really like using `http://dummyimage.com/` for my images in development because it allows me to get any size image, without having to save it.
`http://dummyimage.com/321x123/000/fff` This will return a black image that is 321px by 123px.


Paperclip has the concept of styles, which are basically image styles that are defined in the model.

{% codeblock lang:ruby %}

class User < ActiveRecord::Base
  has_attached_file :profile_picture,
    styles: { mini: "48x48", small: "72x72", medium: "160x160", large: "400x400" },
    url:    "profile_pics/:style/:basename.:extension"
end

{% endcodeblock %}

When I upload an attachment it will save a copy of each of these styles.

Now I could define a path to a custom profile picture for our default url, like so:

{% codeblock lang:ruby %}

class User < ActiveRecord::Base
  has_attached_file :profile_picture,
    styles:      { mini: "48x48", small: "72x72", medium: "160x160", large: "400x400" },
    default_url: "profile_pics/default/:style/default.png",
    url:         "profile_pics/:style/:basename.:extension"
end

{% endcodeblock %}

I dont really want to upload a default image in a bunch of sizes, so I like to use `dummyimage`.

We can create a custom Paperclip interpolation, I explained this in my last blog post, to return the size of image we want based on style.
Paperclip stores a hash of styles and sizes in the attachment options.

{% codeblock lang:ruby %}

Paperclip.interpolates :style_size do |profile_pic, style|
  profile_pic.options[:styles][style]
end

{% endcodeblock %}

This will return the size of image that matches up with the requested style.

{% codeblock lang:ruby %}

class User < ActiveRecord::Base
  has_attached_file :profile_picture,
    styles:      { mini: "48x48", small: "72x72", medium: "160x160", large: "400x400" },
    default_url: "http://dummyimage.com/:style_size/000/fff",
    url:         "profile_pics/:style/:basename.:extension"
end

Paperclip.interpolates :style_size do |profile_pic, style|
  profile_pic.options[:styles][style]
end

{% endcodeblock %}

The only problem left, is that if you dont define a style when requesting the url, it will break. If a style is not defined, Paperclip uses the style :original, which doesnt match up to a size so it will generate a url like:
`http://dummyimage.com/original/000/fff`.
We can fix this easily by defining a size for the interpolation to return to if the style is :original.

{% codeblock lang:ruby %}

class User < ActiveRecord::Base
  has_attached_file :profile_picture,
    styles:      { mini: "48x48", small: "72x72", medium: "160x160", large: "400x400" },
    default_url: "http://dummyimage.com/:style_size/000/fff",
    url:         "profile_pics/:style/:basename.:extension"
end

Paperclip.interpolates :style_size do |profile_pic, style|
  style == :original ? "300x300" : profile_pic.options[:styles][style]
end

{% endcodeblock %}
