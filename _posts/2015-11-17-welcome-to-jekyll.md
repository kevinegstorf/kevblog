---
layout: post
title:  "Testing with Cucumber"
date:   2015-11-17 20:42:03 +0100
categories: Testing
permalink: testing-with-cucumber
---
#cukes_bdd
Is a Cucumber BDD practice application using Ruby on Rails. It's a blog example application. By following the instructions given in this README file, you are able to create this application by using Capybara to simulate user interaction and Cucumber tests for testing purposes. The main goal is to learn how to BDD develop RoR apps by using Cucumber testing.

All the steps I took in this README folder are included in the application.

##Add Cucumber to rails project
To add Cucumber to a rails project you can follow the instructions on the [Cucumber Github page](https://github.com/cucumber/cucumber-rails)

Or you just simply add the code underneath this text to the gemfile folder:

```ruby
group :test do
  gem 'cucumber-rails', :require => false
  # database_cleaner is not required, but highly recommended
  gem 'database_cleaner'
end
```
 and run:

 ```
 $ bundle install
 $ rails generate cucumber:install
 ```

##Adding feature and step_definitions file
After installing Cucumber add a features file in the features folder of the Rails application. For this application we will add a ```blogging.feature``` file the can be  found [here](https://github.com/kevinegstorf/cukes_bdd/blob/master/features/blogging.feature).

when the feature file is included run the following command:

```
$ rake cucumber
```

after running this the command line will return some regular expressions.

```bash
1 scenario (1 undefined)
7 steps (7 undefined)
0m0.010s

You can implement step definitions for undefined steps with these snippets:

Given(/^I am on the home page$/) do
  pending # express the regexp above with the code you wish you had
end

Given(/^I follow "(.*?)"$/) do |arg1|
  pending # express the regexp above with the code you wish you had
end

When(/^I fill in "(.*?)" with "(.*?)"$/) do |arg1, arg2|
  pending # express the regexp above with the code you wish you had
end

When(/^I fill in "(.*?)" with "(.*?)"$/) do |arg1, arg2|
  pending # express the regexp above with the code you wish you had
end

When(/^I press "(.*?)"$/) do |arg1|
  pending # express the regexp above with the code you wish you had
end

Then(/^I should see "(.*?)"$/) do |arg1|
  pending # express the regexp above with the code you wish you had
end
```

These can be copied an pasted in a two files. at ```features/step_definitions```. The name of the file will be ```general_steps.rb```and ```path_steps.rb```.

The code here dirctly underneath goes in to ```path_steps.rb``` and the rest will be used for ```general_steps.rb```.

```ruby
Given(/^I am on the home page$/) do
  pending # express the regexp above with the code you wish you had
end
```

In ```general_steps.rb``` change the block in the regular expression.

- current file

```ruby
Given(/^I follow "(.*?)"$/) do |arg1|
  pending # express the regexp above with the code you wish you had
end
```

- updated file

```ruby
Given(/^I follow "(.*?)"$/) do |link_name|
  pending # express the regexp above with the code you wish you had
end
```

For the other expresssions check this [link](https://github.com/kevinegstorf/cukes_bdd/blob/master/features/step_definitions/general_steps.rb).

when running

```
$rake cucumber
```

the message in the command is updated. there are no more suggestion in regexp but just saying what needs to be implemented.
also add the [Capybara](https://github.com/jnicklas/capybara) gem to the gemfile.

##Updating tests
The Capybara gem provides a way to move around the webpage using code.
Inside the Capybara documentation you can find special keywords we can use in our regexp of the ```features/step_definitions```.

special keywords are for instance:

- fill_in
- click_button

an example used in capybara is:

```ruby
When /I sign in/ do
  within("#session") do
    fill_in 'Email', :with => 'user@example.com'
    fill_in 'Password', :with => 'password'
  end
  click_button 'Sign in'
end
```

for more examples look at the [capybara documentation](https://github.com/jnicklas/capybara).

##Making the first test pass
We will start off by doing step by step the ```path_steps.rb``` test.
As this is a blogging application we will start off by making a route for posts.

change the code inside the file into.

```ruby
Given(/^I am on the home page$/) do
  visit "/posts"
end
```

and run the following command in the commandline.

```bash
$ rake cucumber
```

As you can see this test is not passing. You can read the folliwig test in the commandline.

```bash
 Scenario: Creating a blog post                 # features/blogging.feature:6
    Given I am on the home page                  # features/step_definitions/path_steps.rb:1
      No route matches [GET] "/posts" (ActionController::RoutingError)
      ./features/step_definitions/path_steps.rb:2:in `/^I am on the home page$/'
```

which means we dont have a route that matches posts. to fix this we need to go to ```config/routes.rb``` and update the file so it looks like this:

```ruby
Rails.application.routes.draw do
  resources :posts
end
```

Now run the test again. Now the error message will be.

```bash
  Scenario: Creating a blog post                 # features/blogging.feature:6
    Given I am on the home page                  # features/step_definitions/path_steps.rb:1
      uninitialized constant PostsController (ActionController::RoutingError)
      ./features/step_definitions/path_steps.rb:2:in `/^I am on the home page$/'
```

Which means we need a controller for the posts route. Now run the following cammand on the commandline.

```bash
$ rails generate controller posts
```

Now run the test again. Now the error message will be.

```bash
  Scenario: Creating a blog post                 # features/blogging.feature:6
    Given I am on the home page                  # features/step_definitions/path_steps.rb:1
      The action 'index' could not be found for PostsController (AbstractController::ActionNotFound)
      ./features/step_definitions/path_steps.rb:2:in `/^I am on the home page$/'
```
Which means we do not have a method in the controller file called index.
so lets update the controller file accordingly. so lets go to ```controller/posts_controller.rb```. Inside the file past the followig code.

```ruby
class PostsController < ApplicationController
  def index
  end
end
```

And now run then test again. Now the error message will be.

```bash
Scenario: Creating a blog post                 # features/blogging.feature:6
    Given I am on the home page                  # features/step_definitions/path_steps.rb:1
      Missing template posts/index, application/index with {:locale=>[:en], :formats=>[:html], :variants=>[], :handlers=>[:erb, :builder, :raw, :ruby, :coffee, :jbuilder]}.
```      

Which means we need to add ```index.html.erb``` view in the ```view/posts```. So lets do that.

Now run the test again. It says.

```bash
Scenario: Creating a blog post                 # features/blogging.feature:6
    Given I am on the home page                  # features/step_definitions/path_steps.rb:1
```    


Congratz! You just made your first Cucumber test pass!!
