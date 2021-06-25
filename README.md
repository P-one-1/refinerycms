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

