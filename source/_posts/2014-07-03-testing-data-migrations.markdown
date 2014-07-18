---
layout: post
title: "Testing Data Migrations"
date: 2014-07-03 08:32

---
I'm currently working on migrating legacy data into a new system, which itself is relatively easy, but I want to be able to TDD my migration scripts and thats where it becomes kind of tricky.
For this example our new app will be in Rails and all we have from our old app is SQL Dumps.
It would be cool if ActiveRecord allowed you to use multiple database connections. From what I can tell that's not possible, but with [Sequel](https://github.com/jeremyevans/sequel), it is.
With Sequel, we can initialize two Sqlite databases, one for legacy data and one to store the data that is transfered by our script.

{% codeblock lang:ruby %}
LEGACY_DB = Sequel.sqlite
NEW_DB = Sequel.sqlite
{% endcodeblock %}

We could name these databases like this: `Sequel.sqlite("Some-Database-Name.db")`, but because these are test databases, we dont care to persist any of this data outside of the tests.
First we need to setup our databases.

{% codeblock lang:ruby %}
describe DataMigrator do
  before do
    prep_legacy_db
    prep_new_db
  end
end

def prep_legacy_db
  LEGACY_DB.create_table! :legacy_data_table do # this will drop the database if it already exists, and create a new one
    primary_key :id
    string :name
    date   :birthday
    string :bio, text: true
    string :phone_number
    string :city_name
    string :state_name
  end
end

def prep_new_db
  NEW_DB.create_table! :new_data_table do # this will drop the database if it already exists, and create a new one
    primary_key :id
    string :name
    string :bio, text: true
    string :city
    string :state
    string :phone_number
  end
end
{% endcodeblock %}

Once we have our needed tables, we can write a test that verifies data is being transfered by the migrator.

{% codeblock lang:ruby %}
it "migrates legacy data into the new database" do
  LEGACY_DB[:legacy_data_table].insert(name: "Dwight Schrute", birthday: '01-01-1978', bio: "Paper salesman by day, Beet farmer by night", phone_number: "555-555-5555", city_name: "Scranton", state_name: "PA")
  expect { described_class.new(LEGACY_DB, NEW_DB).migrate! }.to change{ NEW_DB[:new_data_table].count }.by(1)
end
{% endcodeblock %}

Now for the migrator.

{% codeblock lang:ruby %}
class DataMigrator
  def initialize(legacy_db, new_db)
    @legacy_db = legacy_db
    @new_db    = new_db
  end

  def migrate!
    @legacy_db[:legacy_data_table].each do |record|
      @new_db[:new_data_table].insert( name: record[:name], bio: record[:bio], city: record[:city_name], state: record[:state_name], phone_number: record[:phone_number] )
    end
  end
end
{% endcodeblock %}

That should be enough to get us passing, the only problem now is, we need to be able to apply this to an actual app. This get tricky when we have models that use callbacks to setup records.
If you have callbacks that add additional information to the database, they won't get triggered by Sequel inserting data.
We want to figure out a way to use Sequel to pull from the legacy database, and use ActiveRecord models to insert into the new database, all while still using a Sequel connection for testing.
If you look at how data is inserted into a table using Sequel, you can see it is pretty similar to how you create records with ActiveRecord.
I wanted to be able to easily swap out a Sequel database table connection and an ActiveRecord model for running it in production.
I don't really like monkey patching but I think in this case it can help us quite a bit, so we will alias create! to insert in Sequel.

{% codeblock lang:ruby %}
require 'sequel'

module Sequel
  class Dataset
    alias_method :create!, :insert
  end
end
{% endcodeblock %}

For this to work, we will need to modify our tests and migrator to accept tables/models instead of a database connection.

{% codeblock lang:ruby %}
let(:legacy_data_table) { LEGACY_DB[:legacy_data_table] }
let(:new_data_table)    { NEW_DB[:new_data_table] }

it "migrates legacy data into the new database" do
  legacy_data_table.insert({ name:         "Dwight Schrute",
                             birthday:     "01-01-1978",
                             bio:          "Paper salesman by day, Beet farmer by night",
                             phone_number: "555-555-5555",
                             city_name:    "Scranton",
                             state_name:   "PA" })
  expect { described_class.new(legacy_data_table, new_data_table).migrate! }.to change{ new_data_table.count }.by(1)
end
{% endcodeblock %}

{% codeblock lang:ruby %}
class DataMigrator
  def initialize(legacy_data, new_data)
    @legacy_data = legacy_data
    @new_data    = new_data
  end

  def migrate!
    @legacy_data.each do |record|
      @new_data.insert( name: record[:name], bio: record[:bio], city: record[:city_name], state: record[:state_name], phone_number: record[:phone_number] )
    end
  end
end
{% endcodeblock %}

And finally we can change how a new record is created.

{% codeblock lang:ruby %}
def migrate!
  @legacy_data.each do |record|
    @new_data.create!( name: record[:name], bio: record[:bio], city: record[:city_name], state: record[:state_name], phone_number: record[:phone_number] )
  end
end
{% endcodeblock %}

All of our tests should still be passing, and in production we should be able to run something like this:

{% codeblock lang:ruby %}
migrator = DataMigrator.new(LEGACY_DB[:legacy_data_table], DataModel)
migrator.migrate!
{% endcodeblock %}

Even though we have to monkey patch I believe this is a pretty good solution.
I really like being able to have a second database connection that closely mimicks my production database, that I can push data into, without having to load my entire Rails app.

#### Edit

I did hear some feedback that using a Sequel table in test, but using an ActiveRecord model in production, could result in some unintended side effects. I could see this happening, but I have not had any issues yet.
Luckily its very easy to update these tests to use our models. When I started working on this I chose to use Sequel as the test connection to keep my tests fast, but if it causes some side effects,
saving a few seconds on tests is not as important as knowing the tests are accurate.
