---
layout: post
title: "Clojure Testing Frameworks"
date: 2014-03-20 10:57

---

Since I started writing Clojure, I had only used Speclj for testing. I really like Speclj because of how easy it is to write, especially coming from Rspec, but I thought I should give the other main frameworks a try as well.

The first Clojure 'app' I wrote was FizzBuzz and I used Speclj to test it, so I wrote FizzBuzz again for each main framework to have a more even comparison.

#### [Clojure.Test](http://richhickey.github.io/clojure/clojure.test-api.html)

{% codeblock lang:clojure %}
(ns fizzbuzz-testunit.core-test
  (:require [clojure.test :refer :all]
            [fizzbuzz-testunit.core :refer :all]))

(deftest divides?-test
  (testing "is this number a divisor of another number?"
    (testing "that dividies? returns true if the given number is divisible by the given divisor"
      (is (divides? 3 15)))
    (testing "that divides? returns false if the given number is not divisible by the given divisor"
      (is (not (divides? 5 7))))))

(deftest fizzbuzz?-test
  (testing "is this number a fizzbuzz?"
    (testing "that fizzbuzz? should return true if the number is divisible by 3 and 5"
      (is (fizzbuzz? 15)))
    (testing "that fizzbuzz? should return false if the number is not divisible by 3 and 5"
      (is (not (fizzbuzz? 1))))))

(deftest fizz?-test
  (testing "is this number a fizz?"
    (testing "that fizz? should return true if the number is divisible by 3"
      (is (fizz? 3)))
    (testing "that fizz? should return false if the number is not divisible by 3"
      (is (not (fizz? 5))))))

(deftest buzz?-test
  (testing "is the given number a buzz?"
    (testing "that buzz? should return true if the number is divisible by 5"
      (is (buzz? 5)))
    (testing "that buzz? should return false if the number is not divisible by 5"
      (is (not (buzz? 2))))))

(deftest fizzbuzz-test
  (testing "returns appropriate string"
    (testing "that fizzbuzz should return fizzbuzz if the number is divisible by 3 and 5"
      (is (= (fizzbuzz 15) "FizzBuzz!")))
    (testing "that fizzbuzz should return fizz if the number is divisible by 3"
      (is (= (fizzbuzz 3) "Fizz!")))
    (testing "that fizzbuzz it should return buzz if the number is divisible by 5"
      (is (= (fizzbuzz 5) "Buzz!")))
    (testing "that fizzbuzz should return the number if the number is not a fizzbuzz, fizz, or buzz"
      (is (= (fizzbuzz 13) 13)))))
{% endcodeblock %}

First impressions, it feels kind of unnatural to write tests in it, and that may be just because I am coming from Rspec and Speclj, but writing the tests doesn't seem to flow very well for me. On the plus side though, its very basic syntax so it's not difficult to understand, and its pretty apparent what each test is actually testing.

#### [Midje](https://github.com/marick/Midje)

{% codeblock lang:clojure %}
(ns fizzbuzz_midje.t-core
  (:use midje.sweet)
  (:use [fizzbuzz_midje.core]))

(facts "about divides?"
  (fact "it returns true when the given number is evenly divisible by the given divisor"
    (divides? 3 15) => true)
  (fact "it returns false when the given number is not evenly divisible by the given divisor"
     (divides? 2 9) => false))

(facts "about fizzbuzz?"
  (fact "it returns true if the given number is a fizzbuzz"
    (fizzbuzz? 15) => true)
  (fact "it returns false if the given number is not a fizzbuzz"
    (fizzbuzz? 2) => false))

(facts "about fizz?"
  (fact "it returns true if the given number is a fizz"
    (fizz? 3) => true)
  (fact "it returns false if the given number is not a fizz"
    (fizz? 2) => false))

(facts "about buzz?"
  (fact "it returns true if the given number is a buzz"
    (buzz? 5) => true)
  (fact "it returns false if the given number is not a buzz"
    (buzz? 2) => false))

(facts "about fizzbuzz"
  (fact "it returns fizzbuzz if the given number is a fizzbuzz"
    (fizzbuzz 15) => "FizzBuzz!")
  (fact "it returns fizz if the given number is a fizz"
    (fizzbuzz 3) => "Fizz!")
  (fact "it returns buzz if the given number is a buzz"
    (fizzbuzz 5) => "Buzz!"))
{% endcodeblock %}

I have only used it with this FizzBuzz but I really like it. I dont think it flows as easily as Speclj, but I do like the facts/fact syntax and I think that it reads pretty well, when going through the tests.

#### [Speclj](https://github.com/slagyr/speclj/)

{% codeblock lang:clojure %}
(ns fizzbuzz.core-spec
  (:require [speclj.core :refer :all]
            [fizzbuzz.core :refer :all]))

(describe "divides?"
  (it "should return true when the number divides evenly"
    (should (divides? 3 15)))
  (it "should return false when the number does not divide evenly"
    (should-not (divides? 4 7))))

(describe "fizzbuzz?"
  (it "should return true if the number is divisible by 3 and 5"
    (should (fizzbuzz? 15)))
  (it "should return false if the number is not divisible by 3 and 5"
    (should-not (fizzbuzz? 7))))

(describe "fizz?"
  (it "should return true if the number is divisible by 3"
    (should (fizz? 3)))
  (it "should return false if the number is not divisible by 3"
    (should-not (fizz? 1))))

(describe "buzz?"
  (it "should return true if the number is divisible by 5"
    (should (buzz? 5)))
  (it "should return false if the number is not divisible by 5"
    (should-not (buzz? 1))))

(describe "fizzbuzz"
  (it "should return fizzbuzz if the number is divisible by 3 and 5"
    (should= (fizzbuzz 15) "FizzBuzz!" ))
  (it "should return fizz if the number is divisible by 3"
    (should= (fizzbuzz 3) "Fizz!"))
  (it "should return buzz if the number is divisible by 5"
    (should= (fizzbuzz 5) "Buzz!"))
  (it "should return the number if the number isnt fizzbuzz, fizz, or buzz"
    (should= (fizzbuzz 1) 1)))
{% endcodeblock %}

After playing with Midje and Clojure.Test, Speclj is still my favorite, its just super simple to use and the syntax is very familiar to me. I may try and use Midje some though, because I really did like writing tests it in, but I think Speclj will stay as my go-to.
