# WordPress to Mailchimp Role-to-Tag Sync
This guide explains how to use the provided PHP script to automatically update Mailchimp tags whenever a WordPress user's role is changed.

## 1. The PHP Script
Copy the code block below into your child theme's functions.php file or a plugin like Code Snippets.

/**
 * Hook: set_user_role
 * This action fires whenever a user's role is updated in the WordPress database.
 * * @param int    $user_id   The ID of the user being updated.
 * @param string $new_role  The string slug of the new role (e.g., 'subscriber').
 * @param array  $old_roles An array of the user's previous roles.
 */
add_action('set_user_role', 'sync_africaneur_status_to_mailchimp', 10, 3);

function sync_africaneur_status_to_mailchimp($user_id, $new_role, $old_roles) {
    
    // --- SECTION 1: CONFIGURATION ---
    // Update these with your specific Mailchimp account details.
    $api_key       = 'YOUR_API_KEY';      // Found in Mailchimp > Account > Extras > API Keys
    $audience_id   = 'YOUR_AUDIENCE_ID';  // Found in Mailchimp > Audience > Settings
    $server_prefix = 'usX';               // The suffix of your API key (e.g., us12, us20)

    // --- SECTION 2: USER IDENTIFICATION ---
    // Mailchimp identifies subscribers using an MD5 hash of their lowercase email address.
    $user            = get_userdata($user_id);
    $email           = $user->user_email;
    $subscriber_hash = md5(strtolower($email)); 

    // --- SECTION 3: ROLE TO TAG MAPPING ---
    // Key (left) = The WordPress Role Slug.
    // Value (right) = The exact Tag Name as it appears in Mailchimp.
    $role_to_tag_map = [
        'free_africaneur'     => 'Free Africaneur',
        'paid_africaneur'     => 'Paid Africaneur',
        'inactive_africaneur' => 'Inactive Africaneur'
    ];

    // Check if the role being assigned is one of our managed "Africaneur" roles.
    if (!isset($role_to_tag_map[$new_role])) {
        return;
    }

    // --- SECTION 4: CONSTRUCTING THE PAYLOAD ---
    // We loop through our map to create a list of instructions for Mailchimp.
    // We set the status of the NEW role to 'active' and others to 'inactive'.
    $tags_to_send = [];
    foreach ($role_to_tag_map as $role_key => $tag_name) {
        $tags_to_send[] = [
            'name'   => $tag_name,
            'status' => ($role_key === $new_role) ? 'active' : 'inactive'
        ];
    }

    // --- SECTION 5: THE API REQUEST ---
    // Note: URL must be clean without extra brackets or markdown links inside the code.
    $url = "https://{$server_prefix}.api.mailchimp.com/3.0/lists/{$audience_id}/members/{$subscriber_hash}/tags";

    $response = wp_remote_post($url, [
        'method'  => 'POST',
        'headers' => [
            'Authorization' => 'Basic ' . base64_encode('user:' . $api_key),
            'Content-Type'  => 'application/json',
        ],
        'body'    => json_encode(['tags' => $tags_to_send]),
    ]);

    // --- SECTION 6: ERROR LOGGING ---
    // Log failures to wp-content/debug.log.
    if (is_wp_error($response)) {
        error_log('Mailchimp Sync Error: ' . $response->get_error_message());
    }
}

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
