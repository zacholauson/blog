---
layout: post
title: "Basic Spree Defacing"
date: 2014-06-20 13:32

---
Ive been working on a Spree app for a couple of weeks now and I think I am finally starting to get a better understanding of Deface.

To override a view in Spree, you can either overwrite the template completely, or use Deface to add / remove elements from a layout.
Overwriting a layout is really easy because you just put a new template in the location of the template your overwritting in Spree, but in your app.
Deface is almost as convenient, but kind of annoying because you have to either write out your markup and put it in a string in the Deface override, or make a string that renders a new template.
From what I can tell, most people just put all of their markup in a string.

When I first started I wanted to just overwrite the views because I wasn't very interested in working with Deface, because I find putting the markup in a string kind of annoying.
Heres a deface override as an example:

{% codeblock lang:ruby %}

Deface::Override.new(:virtual_path => "spree/admin/shared/_menu",
                     :name => "admin_warehouse_tab",
                     :insert_bottom => "[data-hook='admin_tabs']",
                     :text => "<% if can? :admin, Spree::Admin::WarehousesController %>
                                 <%= tab :warehouses,  :url => spree.admin_warehouses_path, :icon => 'icon-th-large' -%>
                               <% end %>",
                     :disabled => false)

{% endcodeblock %}

This is the same as completely overwriting the view with a file in `app/views/spree/admin/shared/_tabs.html.erb (this partial is loaded by _menu)`

{% codeblock lang:ruby %}
<% if can? :admin, Spree::Order %>
  <%= tab :orders, :payments, :creditcard_payments, :shipments, :credit_cards, :return_authorizations, :icon => 'shopping-cart' %>
<% end %>
<% if can? :admin, Spree::Product %>
  <%= tab :products, :option_types, :properties, :prototypes, :variants, :product_properties, :taxonomies, :icon => 'th-large' %>
<% end %>
<% if can? :admin, Spree::Admin::ReportsController %>
  <%= tab :reports, :icon => 'file'  %>
<% end %>
<%= tab :configurations, :general_settings, :tax_categories, :tax_rates, :tax_settings, :zones, :countries, :states, :payment_methods, :shipping_methods, :shipping_categories, :stock_transfers, :stock_locations, :trackers, :label => 'configuration', :icon => 'wrench', :url => spree.edit_admin_general_settings_path %>
<% if can? :admin, Spree::Promotion %>
  <%= tab(:promotions, :url => spree.admin_promotions_path, :icon => 'bullhorn') %>
<% end %>
<% if Spree.user_class && can?(:admin, Spree.user_class) %>
  <%= tab(:users, :url => spree.admin_users_path, :icon => 'user') %>
<% end %>
<% if can? :admin, Spree::Admin::WarehousesController %>
  <%= tab :warehouses,  :url => spree.admin_warehouses_path, :icon => 'icon-th-large' -%>
<% end %>
{% endcodeblock %}

I prefer overwriting the view completely, especially if I am changing multiple things or adding lots of markup, but that can cause problem if you update Spree and they change that template, because you then wouldn't get that updated template.
