---
layout: post
title: "Custom Paperclip Interpolations"
date: 2014-07-17 20:30

---
Paperclip gives you a pretty wide range of built in interpolations for storing your file in dynamic paths,
but sometimes you need to be able to interpolate some data that Paperclip doesn't know how to get.
I recently came upon this issue and thankfully Paperclip made it very easy to add custom interpolations.

Lets start with our model that has a paperclip attachment.

{% codeblock lang:ruby %}

class Store < ActiveRecord::Base
  has_attached_file :floorplan,
    :path => ":rails_root/storage/floorplans/:basename.:extension",
    :url  => ":basename.:extension"
end

{% endcodeblock %}

Paperclip makes it pretty simple to get the attachment setup, but now our app is growing and we to add some relationships.

{% codeblock lang:ruby %}

class Store < ActiveRecord::Base
  belongs_to :warehouse

  has_attached_file :floorplan,
    :path => ":rails_root/storage/floorplans/:basename.:extension",
    :url  => ":basename.:extension"
end

{% endcodeblock %}

Still pretty simple, but at this point we probably should organize our uploads a little better than by the files name.
The Store belongs to a warehouse, so we can scope the uploads by the warehouse the store belongs to.

Ideally we would like a path that looks something like this:

{% codeblock lang:ruby %}

:path => ":rails_root/storage/:warehouse_name/floorplans/:basename.:extension"

{% endcodeblock %}

Paperclip cant just search for a method and use it for interpolations, but it allows us to define custom interpolations with: `Paperclip.interpolates`

A Paperclip interpolation starts with the interpolation name, the symbol used in the interpolation, the attachment itself, and the style.

{% codeblock lang:ruby %}

Paperclip.interpolates :warehouse_name do |floorplan, style|
end

{% endcodeblock %}

With the attachment we can get the instance of the object the attachment is on

{% codeblock lang:ruby %}

Paperclip.interpolates :warehouse_name do |floorplan, style|
 floorplan.instance #=> our Store record
end

{% endcodeblock %}

With that we can get our parent warehouse, and its name.

{% codeblock lang:ruby %}

Paperclip.interpolates :warehouse_name do |floorplan, style|
 floorplan.instance.warehouse.name
end

{% endcodeblock %}

Now we can either include this interpolation in our model or drop it into an initializer.
I think its probably best to leave specific interpolations like this in the model, in case we need to use `:warehouse_name` again somewhere else.

{% codeblock lang:ruby %}

class Store < ActiveRecord::Base
  belongs_to :warehouse
  has_attached_file :floorplan,
    :path => ":rails_root/storage/:warehouse_name/floorplans/:basename.:extension"
    :url  => ":basename.:extension"
end

Paperclip.interpolates :warehouse_name do |floorplan, style|
 floorplan.instance.warehouse.name
end

{% endcodeblock %}

I would probably add one more interpolation in this case, just to make sure everything is pretty well organized.

{% codeblock lang:ruby %}

class Store < ActiveRecord::Base
  belongs_to :warehouse
  has_attached_file :floorplan,
    :path => ":rails_root/storage/:warehouse_name/floorplans/:store_name.:extension"
    :url  => ":basename.:extension"
end

Paperclip.interpolates :warehouse_name do |floorplan, style|
 floorplan.instance.warehouse.name
end

Paperclip.interpolates :store_name do |floorplan, style|
  floorplan.instance.name
end

{% endcodeblock %}

Now all of our store floorplans would be organized by the stores name instead of the file name.
