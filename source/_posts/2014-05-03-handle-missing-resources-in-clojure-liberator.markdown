---
layout: post
title: "Handle Missing Resources in Clojure Liberator"
date: 2014-05-03 13:18

---
I was looking for a good way to manage missing resources in my API with Clojure Liberator and I found that the documenation was lacking in this area.

I found the `:exists?` and `:existed?` tags but I couldnt find a way to check if the resource exists and return the resource without doing 2 queries to my database.

After a while of searching I found a strange double colon keyword mentioned in an issue on Liberator's Github.

I played around with it for a while and figured out you can set your returned resource to this double colon keyword in the `:exists?` function and then return that resource in the `:handle-ok`

#####Example:

{% codeblock lang:clojure %}

(defresource resource [id]
  :available-media-types ["application/json"]
  :exists? (fn [& _]
             (if-let [resource (first (select resource (where {:id (str->int id)})))]
               {::resource resource}))
  :handle-ok ::resource
  :handle-not-found (fn [& _] "No Resource Found."))

{% endcodeblock %}
