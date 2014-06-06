---
layout: post
title: "Spree Custom Models"
date: 2014-06-05 22:09

---
Ive been working with Spree for the past couple of weeks, and it has certainly been interesting. Spree is an eCommerce framework/platform in the form of a Rails engine.
It provides a lot of functionality, which is nice but can make it difficult when adding custom components.

In this example we will be creating a custom Warehouse model that has products.

To create a new model we can first make the migration, we will also need a join table for the relationship between a Warehouse and a Product, so we can create that now as well.

{% codeblock lang:sh %}
  rails g migration CreateSpreeWarehouses
  rails g migration CreateSpreeWarehousesProducts
{% endcodeblock %}

{% codeblock lang:ruby %}
class CreateSpreeWarehouses < ActiveRecord::Migration
  def change
    create_table :spree_warehouses do |t|
      t.string :name
      t.text   :description
      t.timestamps
    end
  end
end
{% endcodeblock %}

{% codeblock lang:ruby %}
class CreateSpreeWarehousesProducts < ActiveRecord::Migration
  def change
    create_table :spree_warehouses_products do |t|
      t.belongs_to :warehouse, class_name: "Spree::Warehouse"
      t.belongs_to :product,   class_name: "Spree::Product"
    end
  end
end
{% endcodeblock %}

Then we can create the models for these two new tables.

{% codeblock app/models/spree/warehouse.rb lang:ruby %}
module Spree
  class Warehouse < ActiveRecord::Base
    has_many :warehouses_products, class_name: "Spree::WarehousesProducts"
    has_many :products, class_name: "Spree::Product", through: :warehouses_products
  end
end
{% endcodeblock %}

{% codeblock app/models/spree/warehouses_products.rb lang:ruby %}
module Spree
  class WarehousesProducts < ActiveRecord::Base
    belongs_to :warehouse, class_name: "Spree::Warehouse"
    belongs_to :product,   class_name: "Spree::Product"
  end
end
{% endcodeblock %}

Everything is standard rails, but if you want to interact with things built into spree, we need to decorate spree's models.
So to add the association on Spree's model, we can class eval Spree::Product and add our associations.

{% codeblock app/models/spree/product_decorator.rb lang:ruby %}
module Spree
  Product.class_eval do
    has_many :warehouses_products, class_name: "Spree::WarehousesProducts"
    has_many :warehouses,          class_name: "Spree::Warehouse"
  end
end
{% endcodeblock %}

We can test the association.

{% codeblock %}

Loading development environment (Rails 4.0.5)
irb(main):001:0> Spree::Warehouse
=> Spree::Warehouse(id: integer, name: string, description: text, created_at: datetime, updated_at: datetime)
irb(main):002:0> Spree::WarehousesProducts
=> Spree::WarehousesProducts(id: integer, warehouse_id: integer, product_id: integer)
irb(main):003:0> Spree::Product
=> Spree::Product(id: integer, name: string, description: text, available_on: datetime, deleted_at: datetime, slug: string, meta_description: text, meta_keywords: string, tax_category_id: integer, shipping_category_id: integer, created_at: datetime, updated_at: datetime)
irb(main):004:0> Spree::Warehouse.create(name: "Warehouse 1")
   (0.1ms)  begin transaction
  Spree::Warehouse Exists (0.2ms)  SELECT 1 AS one FROM "spree_warehouses" WHERE "spree_warehouses"."slug" = 'warehouse-1' LIMIT 1
  SQL (3.6ms)  INSERT INTO "spree_warehouses" ("created_at", "name", "updated_at") VALUES (?, ?, ?)  [["created_at", Fri, 06 Jun 2014 03:40:14 UTC +00:00], ["name", "Warehouse 1"], ["updated_at", Fri, 06 Jun 2014 03:40:14 UTC +00:00]]
   (2.2ms)  commit transaction
=> #<Spree::Warehouse id: 1, name: "Warehouse 1", description: nil, created_at: "2014-06-06 03:40:14", updated_at: "2014-06-06 03:40:14">
irb(main):005:0> warehouse =_
=> #<Spree::Warehouse id: 1, name: "Warehouse 1", description: nil, created_at: "2014-06-06 03:40:14", updated_at: "2014-06-06 03:40:14">
irb(main):006:0> warehouse.products
  Spree::Product Load (0.2ms)  SELECT "spree_products".* FROM "spree_products" INNER JOIN "spree_warehouses_products" ON "spree_products"."id" = "spree_warehouses_products"."product_id" WHERE "spree_products"."deleted_at" IS NULL AND "spree_warehouses_products"."warehouse_id" = ?  [["warehouse_id", 1]]
=> #<ActiveRecord::Associations::CollectionProxy []>
{% endcodeblock %}

Everything looks good, I think for my next blog post Ill talk about setting up controllers for this resource, because using Spree::ResourceController, we can save a lot of work, if we are okay with some magic.
