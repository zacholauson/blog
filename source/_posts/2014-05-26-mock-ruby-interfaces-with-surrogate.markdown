---
layout: post
title: "Mock Ruby Interfaces with Surrogate"
date: 2014-05-26 11:42

---
While working with objects that should be easily replaceable for each other, it is nice to inforce specific rules on those objects.
Like if we have several Filter classes, that will be passed around to class that dont care about each filters specific implementations, it would be nice to make sure each Filter class implements specific methods, like how Java does with Interfaces.

Ruby doesnt really have this but there is a gem that allows us to do that. Its called [Surrogate](https://github.com/JoshCheek/surrogate), and is designed for creating Mocks, but can be used to put constraints on other classes to make sure classes can be substituted easily.

Following the example of Filters, lets say we start with a MockFilter using Surrogate.

We should be able to initialize a filter with a optional hash of options, and we want each filter to have a method called `execute` and takes 1 argument, a filterable array.

{% codeblock lang:ruby %}

require 'surrogate'

class MockFilter
  Surrogate.endow self

  define :initialize do |options={}|
  end

  define :execute do |filterable_array|
  end
end

{% endcodeblock %}

That should be enough to enforce the constraints we want.

Now we can work on setting up our implemeted classes, so lets write some tests.

{% codeblock lang:ruby %}

require 'surrogate/rspec'
require 'filters/state_filter'
require 'mock_filter'

describe Filters::StateFilter do
  it "implements our filter interface" do
    Filters::StateFilter.should substitute_for MockFilter
  end
end

{% endcodeblock %}

Thats enough to get us started, Surrogate will tell us that to allow Filters::StateFilter to substitute for MockFilter, we will need to implemented an initialize method, and a execute method.

{% codeblock lang:ruby %}

module Filters
  class StateFilter
    def initialize(options={})
    end

    def execute(filterable_array)
    end
  end
end

{% endcodeblock %}

