---
layout: post
title: "Creating a WordPress Plugin Settings Page"
date: 2014-08-13 14:41:18 -0400
comments: true
categories: WordPress
---
In my last [post](http://lcbecker.github.io/blog/2014/08/12/wordpress-short-codes-for-dummies/), I showed you how to make a WordPress short code plugin. In this post, I'll build upon that short code and teach you how to make a simple settings page.

### A Little Backstory ###
I found the resources currently available on the web on plugin settings pages extremely frustrating and convoluted for someone's first foray into WordPress development. I was continually pointed to [Otto's tutorial](http://ottopress.com/2009/wordpress-settings-api-tutorial/). Otto's tutorial works. It just isn't clear to beginners why it works or how to modify it for your own purposes. I believe it's always nice just to see a simple tutorial on how to create a plugin settings page before you start introducing how to do all the bells and whistles that can be implemented. This is the only [site](http://code.tutsplus.com/articles/how-to-integrate-an-options-page-into-your-wordpress-theme--net-11847) I found that really helped me understand how to create a settings page, and it's to create custom settings for themes. Still, I was able to use it to make a nice, working plugin settings page and for that, I am extremely grateful! My tutorial is to teach you what I've learned in hopes that you will struggle less than I did.

### Getting Started: Creating The Settings Menu and Page ###
There two things we need to do. 1. Add a submenu to the admin page. Our settings page will be found under Settings -> Signature Plugin Settings after all is said and done. 2. Create the settings page itself. Since we're building upon the Signature plugin from my last post, you can add all of the following code right into that same PHP file, directly under the `add_shortcode` call.

So, let's add a submenu and begin creating our settings page:

	add_action('admin_menu', 'sig_admin_add_page');

	function sig_admin_add_page() {
		add_options_page('Signature Plugin Settings', 'Signature Plugin Settings', 'manage_options', 'sig-plugin', 'sig_contents');
	}
	
What does all this code mean/do? Well, the first line tells WordPress to add a submenu to the admin page and to create it using the `sig_admin_add_page` function. This function calls the `add_options_page` method which sets the text to be displayed in the menu, the page title for your settings page, the level of capability needed by the user in order to even see this menu option, the slug (end of URL) for the settings page, and finally, the function to call to generate the content of your settings page.

### Creating Your Setting Page's Content ###

Remember how the signature's default first name was "Firstname" and the last name was "Lastname"? What if we could let the user set the defaults using a plugin settings page? In order to set the first and last name defaults, we need a form where the user can type in what they want and then save what the user types in. Saving the user input requires us to use the WordPress database. So, we'll do a POST to our form when the user clicks the Save Changes button. If a POST occurs (which it should), it will call another function, `sig_options_update()` to save the values to the WordPress database. Let's see what this all looks like:

	function sig_contents() {
	
		if ( $_POST['update_sigoptions'] == 'true' ) {
			sig_options_update();
		}
		?>
		
		<div class="wrap">
		<h2> Signature Plugin Settings </h2>
		<form action="" method="POST">
			<input type="hidden" name="update_sigoptions" value="true" />
		<h4> Change the default first and last name used: </h4>
		<table>
		<tr>
		<td><label for='firstname'> First name: </label></td>
		<td><input id='firstname' name='first' size='40' type='text' value='<?php echo get_option("first_name"); ?>' /></td>
		</tr>
		</tr>
		<td><label for='lastname'> Last name: </label></td>
		<td><input id='lastname' name='last' size='40' type='text' value='<?php echo get_option("last_name"); ?>' /></td>
		</tr>
		</table>
		<br>
		<p><?php if (!empty($_POST['first])) { echo '<h4 style="text-align:center;"> The first and last name that will be used as the default signature is: ' . $_POST["first"] . " " . $_POST["last"] . '</h4>'; } ?></p>
			<?php submit_button() ?>
		</form>
	</div>
	<?php
	}
	
	function sig_options_update() {
		update_option('first_name', $_POST['first']);
		update_option('last_name', $_POST['last']);
	}
	
Phew, that was a lot of code, but it's all easy stuff. We're simply providing input boxes for the first and last names, then saving those values in the database once a POST has been called via the submit button. 

Now that we can set default values for the first and last names and save them in the WordPress database, we need to use those values! And so we must modify our code for our plugin that we wrote earlier.

### Modifying The Signature Plugin ###

##### If you're not using attributes ####
To use our saved database values, all we need to do is modify the `add_signature()` function, like so:

	function add_signature() {
	
		// Gets values from the WordPress database.
		$first = get_option('first_name');
		$last = get_option('last_name');
		
		// If we don't have something saved in the database for the first name, we need to give it some value…in this case, "Firstname".
		if (empty($first)) {
			$first = "Firstname";
		}
		
		if (empty($last)) {
			$last = "Lastname";
		}
		
		return '<p> Sincerely, ' . $first . ' ' . $last '</p>';
	}

After adding this new code, you'll need to rezip and install your updated plugin into WordPress. If you're using WordPress locally through something like XAMPP, just copy the updated PHP file into wordpress/wp-content/plugins/signature folder you made previously.

##### If you are using attributes #####
This may be some ugly code (sorry!), but we need it in order to check for certain conditions. Are there default values stored in the database? If not, we need to provide something. Were there attributes? If so, let's use those instead. So, our new `add_atts` function looks like this:

	function add_atts($first, $last) {
		$name1;
		$name2;
		
		// Gets values from the WordPress database.
		$data_first = get_option('first_name');
		$data_last = get_option('last_name');
		
		if (empty($first)) { // If no first name attribute was provided
			if (empty($data_first)) { // If no  default first name is saved in the database
				$name1 = "Firstname"; // Firstname = "Firstname"
			} else {
				$name1 = $data_first; // If a default first name is saved, use it.
			}
		} else {
			$name1 = $first; // If a first name attribute was provided, use it.
		}
		
		if (empty($last)) {
			if (empty($data_last)) {
				$name2 = "Lastname";
			} else {
				$name2 = $data_last;
			}
		} else {
			$name2 = $last;
		}
		
		return $name1 . " " . $name2;
	}

After adding this new code, you'll need to rezip and install your updated plugin into WordPress. If you're using WordPress locally through something like XAMPP, just copy the updated PHP file into wordpress/wp-content/plugins/signature folder you made previously.

### Testing And Troubleshooting ###
When I first made a WordPress plugin, I got an error when I installed and activated it…a warning about headings appeared. If this is your first time making a plugin and you're not familiar with coding in PHP, you might not know that leaving spaces after the end of a block of PHP code creates this odd heading error in WordPress. Just make sure that there is no extra line or lines after the `?>` at the end of your file. This should clear that error up.

Second, you might want to dive into the WordPress database and see for yourself if you've set the `first_name` and `last_name` options! Since we didn't use arrays to save our values, you can actually see them (values saved in an array show up as SERIALIZED…not very helpful). Login to WordPress and after wp-admin/ add options.php. This shows you every value saved in the WordPress database. If you haven't played around with your settings page yet, `first_name` and `last_name` won't be in there. But if you have, they will be there in the list as well as their associated values.

### Final Thoughts ###
Now you know how to make a short code plugin (with or without attributes) and a settings page for it that saves values in the WordPress database. I've tried to show you how to do this as simply as possible with descriptions that explain exactly what we're doing and why. Hopefully, you can now use this knowledge to create your own plugin and have it do what you want. Happy coding!
