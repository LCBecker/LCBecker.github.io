---
layout: post
title: "WordPress Short Codes For Dummies"
date: 2014-08-12 08:17:27 -0400
comments: true
categories: WordPress
---
My most recent project as a junior developer intern was to create a WordPress short code and an admin settings page for it. I thought WordPress was supposed to be simple to work with, but as I found out, the resources out there on the subject of short codes and creating settings pages are quite murky if you've never done WordPress development before. So, in this post, I'll teach you the basics of making a short code plugin.

### Create A Short Code ###
The first thing you need to know about making a short code plugin is that WordPress requires a commented heading at the very top of the file and uses it to display information about your plugin when you install/activate it. For this short tutorial, let's say you want to add a signature to the end of your posts, just a nice little "Sincerely, Firstname Lastname" for a personalized touch. So, create a new PHP file and create the heading:

	<?php
	/*
	Plugin Name: Signature    
	Plugin URI: http://ifyouhaveone.com
    Description: Adds a simple signature to your posts and pages.
    Version: 1.0
    Author: YOU!
    Author URI: http://ifyouhaveone.com
    License: GPL or None or Whatever
    */

Now, we create the HTML to add a signature by adding a PHP function in our new file with the nice heading:

    function add_signature() {
        return '<p> Sincerely, Firstname Lastname </p>';
    }
Wow, that was simple. Good. Next, we initialize the short code that will execute this function by calling the WordPress function `add_shortcode`. This function requires two parameters. The first one is what will be what you want users to type inside short code brackets to run your code. The second is the function to call to actually add the signature. Like so:

    add_shortcode('sig', 'add_signature');
    
In this case, typing `[sig]` on a WordPress post or page will add our signature by calling the `add_signature` function we just wrote. 

Your plugin is almost ready! Feeling adventurous? Want to learn how to make this short code take attributes? Then click [here](#attr). 

Otherwise, you probably want to see your plugin in action, just to check if I'm totally off my rocker and leading you astray. So, let's finalize our PHP file by adding `?>` on the very last line. Finally, zip your PHP file in your file manager or via command line for you fancy people out there.

###[Installing Your Short Code](id:install) ###

Log into your WordPress account. From the left-hand menu, select Plugins --> Add New. Under the Install Plugins header, click on Upload. Click on Choose File, find your zipped short code file on your system, then click install now. If you have the ability to activate your short code after successful installation, do so. Otherwise, from the left-hand menu, select Plugins --> Installed Plugins. Find the Signature plugin and click Activate. Great! Now, you can use it!

If you are running WordPress locally (such as through XAMPP), the above directions will not work. In this case, simply copy your PHP file and paste it into your WordPress/wp-content/plugins folder. Then from your WordPress account, select Plugins --> Installed Plugins and activate it.

To use your new short code, simply add `[sig]` where you'd like your signature to appear on a page or post. If you used attributes, you now know the other ways to customize your signature via the short code macro. Congrats!


###[Adding Attributes To Your Short Code](id:attr) ###
Attributes are a nice way to customize your plugin on the fly. For our example, let's say you don't always want your signature to simply be Firstname Lastname. What if you could change that within each post, depending on your mood? Well you can--with attributes!

In order to allow and get attributes, we need to modify our `add_signature` function. We need our function to take in an `$atts` parameter and extract from it some attributes. In our case, we probably want to set the first and last name that's displayed. So, we'll do that by adding attributes to our short code and processing them in a new function we'll call `add_atts`.

Let's take a look at our new and improved `add_signature` function:

	function add_signature($atts) {
	    extract(shortcode_atts(array(
		    'first' => '',
		    'last' => '',
		), $atts));

        return '<p> Sincerely, ' . add_atts($first, $last) . '</p>';
    } 
    
Oh, nice! Inside the array, we declare each possible attribute we could set - either the first name('first') or the last name('last'). These attributes can now be used as the variables `$first` and `$last`, which we pass as parameters to the function `add_atts`. Let's see what that function looks like:

	function add_atts($first, $last) {
	    $name1;
		$name2;

		if (empty($first)) {
			$name1 = "Firstname";
		} else {
			$name1 = $first;
		}

		if (empty($last)) {
			$name2 = "Lastname";
		} else {
			$name2 = $last;
		}

		return $name1 . " " . $name2;
	} 
What does this function do? Well, if we don't specify a first or last attribute in our short code, it will print a default value, in this case "Firstname" and "Lastname" respectively. If we specify one or both of them, our attribute values will show up. In other words,

* `[sig]` will return Sincerely, Firstname Lastname
* `[sig first="Happy"]` will return Sincerely, Happy Lastname
* `[sig last="Gilmore"]` will return Sincerely, Firstname Gilmore
* `[sig first="Happy" last="Gilmore"]` will return Sincerely, Happy Gilmore

Ahh, that's exactly what we wanted! And it's really rather simple! So, now it's time to install this plugin and get it up and running. So, let's finalize our PHP file by adding `?>` on the very last line. Finally, zip your PHP file in your file manager or via command line for you fancy people out there. Now head over to [Installing Your Short Code](#install).

### Final Thoughts ###
So now that you've made a short code plugin, there's a few other things I should tell you. If you want to publish your plugin with WordPress Plugin Directory, you need a README file with much of the same information as the comments on the top of your PHP file. If you want more on creating short codes, I highly recommend [this site](http://www.smashingmagazine.com/2012/05/01/wordpress-shortcodes-complete-guide/) for additional help.

Soon, I will show you how to create a plugin settings page. Stay tuned! 