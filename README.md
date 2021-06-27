## Getting Started
https://www.refinerycms.com/guides/getting-started


# Refinery's architecture
Refinery is comprised of several Rails Engines. Each engine, or "extension" acts like a mini Rails application with its own routes and views. Refinery is architected like this so that it keeps out of the way of any custom development you will do in the /app directory.

# Core extensions
The extensions Refinery comes with are:

* Core - contains default layouts, views, javascripts and CSS. This extension also has an extension API for extending Refinery and everything Refinery needs to hook into Rails.

* Images - handles image upload, insertion and processing images using Dragonfly.

* Pages - allows you to manage pages including the structure of your site displayed in the front-end.

* Resources - handles file upload and storage.

* Authentication (optional) - manages users and sessions within Refinery.

# Setting Your Site Name
You'll need to set your Site Name; it's used in several spots on the CMS to give you nice branding (for instance, in the blue header at the top of the page and in the footer of your site).
To do this, you'll have to edit config/initializers/refinery/core.rb. Look for the line that begins:
config.site_name = "Company Name"

TIP: Many parts of Refinery can be customized by changing the options contained within the config/initializers/refinery/ folder. As you add extensions to Refinery, more files will be created here specific to the extensions you install.

# Overriding your first view
By default Refinery has a range of views built in to display the front-end site you currently have. But more times than often you want to customise them with your own layout and design.

TIP: Overriding Refinery views is a common pattern which is worth keeping in mind at all times. If Refinery isn't displaying something how you'd like, just override it.

If you request http://localhost:3000/about, this maps by default to Refinery's pages_controller.rb show action.
So as you would expect according to Rails convention, the view for this action is located in app/views/refinery/pages/show.html.erb. You won't be able to see this file because Refinery is providing it for you. Next, let's override that view and replace it with our own.

TIP: Overriding a file simply copies the file from Refinery's code into your app/ folder. Many people are confused as to what can be overridden initially. Any controller, model, view, javascript, or stylesheet from any installed extension can be overridden, but the most commonly overridden ones are those in the refinery folder.

* Specify a different Refinery::Core::Engine mount path than the default of "/".

    config.mounted_path = "/blog"



## Generate an Extension to Use Your MVCs

# Generating an extension
Refinery ships with an extension generator that makes adding your own functionality a breeze. It works just like the Rails scaffold generator: given information about a model, it will automatically generate the necessary model, view, controller, configuration, and database files for you and will put them into their appropriate directories. Here's the command:
$ rails generate refinery:engine singular_model_name attribute:type [attribute:type ...]
TIP: to see all the options supported by the refinery:engine generator just run rails g refinery:engine.
$ rails generate refinery:engine event title:string date:datetime photo:image blurb:text
As the output shows, next run these commands:
$ bundle install
$ rails generate refinery:events
$ rake db:migrate
$ rake db:seed
When you run bundle install, you may see a message that says
...extensions/events did not have a valid gemspec. This prevents bundler from installing bins or native extensions, but that may not affect its functionality. The validation message from Rubygems was:
   authors may not be empty

This is a warning message from RubyGems telling you that no author is specified for this extension. This is only important if you are going to create a gem from this extension. You can safely ignore this message. If you do want to resolve it and get rid of the message, you can edit the gem specification file for the extension refinerycms-events.gemspec and put your name in there as the author. Just put this line: s.author = 'yourname' in the do block (after the s.version.... line is fine).
TIP: Models in Refinery extensions expect a string field that acts as the title identifier when displayed in lists in the admin pages. If a title field is not included, the first string field found will be used. Models without a usable field for a title will cause the admin to raise an error, so please include a title field or alias when creating models in your extension.

You'll see the entire form has been generated for you based off the field types you specified when generating the events section. The blurb has a visual editor, the date field is a date picker and the photo allows you to pick or upload a new photo from a built-in Refinery dialog.
Add a couple of mock events to your events extension.
Now click on "Switch to your website", and navigate to http://localhost:3000/events
You'll notice not only has Refinery generated the backend "Events" tab but also a new menu item called "Events" and two new front-end views,index.html.erb and show.html.erb, located in vendor/extensions/events/app/views/refinery/events/ for you to customise.

# Testing your extension
There is a separate guide which covers this subject found at Testing Your Extension(https://www.refinerycms.com/guides/testing/).


# Crudify: The Backbone of Refinery Engines
Any Refinery extension, even the built-in ones, that focus on Create, Read, Update and Delete are driven by crudify. Crudify is a highly reusable module included with Refinery that gives you all the standard CRUD actions as well as reordering, searching and paging.
Open up vendor/extensions/events/app/controllers/refinery/events/admin/events_controller.rb and look at its contents:
module Refinery
  module Events
    module Admin
      class EventsController < ::Refinery::AdminController
        crudify :'refinery/events/event', :xhr_paging => true
      end
    end
  end
end

Most of the time, crudify's defaults are bang on, but if you need to, you can easily customise how it works.
By default crudify assumes your records will be sortable. But events should not be manually sortable; it makes more sense to order them by their event date. Update the contents of the file to this:
module Refinery
  module Events
    module Admin
      class EventsController < ::Refinery::AdminController
        crudify :'refinery/events/event', :xhr_paging => true,  :order => "date DESC",
                                          :sortable => false
      end
    end
  end
end

This will tell crudify to sort by our event date field and to turn off manual sorting by the user.
Finally edit vendor/extensions/events/app/controllers/refinery/events/events_controller.rb and replace the find_all_events method with this one:
module Refinery
  module Events
    class EventsController < ::ApplicationController
      # code
      protected
        def find_all_events
          # Order by event date
          @events = Event.order("date DESC")
        end
        # code
    end
  end
end

Now when you look at http://localhost:3000/events you'll notice they're now being sorted by the event date.
