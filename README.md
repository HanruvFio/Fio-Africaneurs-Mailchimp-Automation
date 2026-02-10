# Africaneur User Flow: WordPress + Mailchimp Setup

## 🛠 Part 1: FuseWP Configuration
This ensures your WordPress roles stay in sync with your Mailchimp audience tags without creating duplicates.

### 1. Connect Mailchimp
1. Log into **WordPress Admin**.
2. Go to **FuseWP > Settings
3. Find **Mailchimp** and click **Configure**.
4. Log into your mailchimp and connect it.
5. Save Changes. Ensure the status shows a green **Connected** light.

### 2. Sync Rules (The "Tag Swap")
Go to **FuseWP > User Sync > Add New**. Create these three rules. 

**Crucial:** For all three rules, ensure "Email Address" is mapped to "Email Address".

| Rule Name | Source (WP Role) | Action (Mailchimp) | Setting to Enable |
| :--- | :--- | :--- | :--- |
| **Status: Free** | Free Africaneur | Add Tag: `Free Africaneur` | "Remove user from destination when they no longer belong to this role" |
| **Status: Paid** | Paid Africaneur | Add Tag: `Paid Africaneur` | "Remove user from destination when they no longer belong to this role" |
| **Status: Inactive**| Inactive Africaneur | Add Tag: `Inactive Africaneur`| "Remove user from destination when they no longer belong to this role" |

### How this works in practice:
* **When they register:** They get the `Free Africaneur` tag.
* **When you upgrade them to Paid:** FuseWP sees they are no longer "Free" and **removes** the `Free` tag, then sees they are now "Paid" and **adds** the `Paid` tag.
* **When they lapse to Inactive:** FuseWP **removes** the `Paid` tag and **adds** the `Inactive` tag.
* **Result:** The contact stays in Mailchimp the whole time; only the label changes.

---

## 🧪 Part 2: The Testing Roadmap
Use a personal email address that is **not** currently a user on the site.

### Phase 1: The Initial Registration (Free)
1. **Action:** Open an **Incognito Browser Window**.
2. **Action:** Go to your registration page and sign up as a new user.
3. **Verify (WP):** Check **Users > All Users**. Ensure the email is assigned the **Free Africaneur** role.
4. **Verify (Mailchimp):** Search for the email in Mailchimp. Ensure the contact has the tag **`Free Africaneur`**.
5. **Verify (Frontend):** Try to visit a "Paid Only" page. You should be blocked.

### Phase 2: The Backend Upgrade (Paid)
1. **Action:** In WP Admin, edit your personal user account.
2. **Action:** Change the Role from **Free Africaneur** to **Paid Africaneur**. Save.
3. **Verify (Mailchimp):** Refresh Mailchimp. The `Free` tag should be gone, and the **`Paid Africaneur`** tag should appear.
4. **Verify (Frontend):** Refresh your site as the personal user. Premium content should now be visible.

### Phase 3: The Subscription Lapse (Inactive)
1. **Action:** In WP Admin, change the user's role to **Inactive Africaneur**. Save.
2. **Verify (Mailchimp):** Refresh Mailchimp. The tag should now be **`Inactive Africaneur`**.
3. **
