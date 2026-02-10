# WordPress to Mailchimp Role-to-Tag Sync
This guide explains how to use the provided PHP script to automatically update Mailchimp tags whenever a WordPress user's role is changed.

## 1. The PHP Script
Copy the code block below into your child theme's functions.php file or a plugin like Code Snippets.



## 2. Detailed Explanation
### The Hook (set_user_role)
The script uses a WordPress "Action Hook." Whenever a user's role is modified (via the admin dashboard or another plugin), WordPress executes our function and passes the user's ID and their new role.

### The Subscriber Hash
Mailchimp’s API uses a unique way to identify users in a URL. Instead of sending the email address directly, we must send a lowercase MD5 hash of the email.

Example: Hello@Example.com becomes hello@example.com → eb5df50...

### The Logic (The "Tag Swap")
Instead of just adding a tag, this code performs a clean swap. It looks at your $role_to_tag_map. It tells Mailchimp to:

### Activate the tag associated with the new role.

Inactivate the tags associated with the other roles. This ensures a user doesn't end up with both a "Free" and "Paid" tag simultaneously.

## 3. Implementation & Testing
Step 1: Setup
API Key: In Mailchimp, go to Account > Extras > API Keys.

Audience ID: Go to Audience > Settings > Audience name and defaults. Look for the "Audience ID" (usually a string of letters and numbers).

Server Prefix: Look at your API key; if it ends in -us20, your prefix is us20.

### Step 2: Deployment
Add the code to your site. Ensure the roles in the $role_to_tag_map (the left side) match the exact slugs of your WordPress roles.

### Step 3: Testing
Open your WordPress Users list.

Find a test user that already exists in your Mailchimp audience.

Change their role to Paid Africaneur and save.

Check the user in Mailchimp. You should see the tag update within seconds.

Change them back to Free Africaneur. Verify that the "Paid" tag is removed and the "Free" tag is added.
