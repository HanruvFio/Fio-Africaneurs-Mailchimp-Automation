# Africaneur User Flow: WordPress + Mailchimp Setup

## 🛠 Part 1: FuseWP Configuration
This ensures your WordPress roles stay in sync with your Mailchimp audience tags without creating duplicates.

### 1. Connect Mailchimp
1. Log into **WordPress Admin**.
2. Go to **FuseWP > Settings
3. Find **Mailchimp** and click **Configure**.
4. Log into your mailchimp and connect it.
5. Save Changes. Ensure the status shows a green **Connected** light.

### 2. Create Sync Rules
Go to **FuseWP > User Sync > Add New** and create these three rules:

| Rule Name | Source (WP Role) | Destination (Mailchimp Tag) | Special Setting |
| :--- | :--- | :--- | :--- |
| **Free Sync** | Free Africaneur | `Free Africaneur` | Enable: "Remove user when role changes" |
| **Paid Sync** | Paid Africaneur | `Paid Africaneur` | Enable: "Remove user when role changes" |
| **Inactive Sync** | Inactive Africaneur | `Inactive Africaneur` | Enable: "Remove user when role changes" |

> **Pro Tip:** Enabling "Remove user when role changes" is what prevents tag clutter. It ensures a user only ever has **one** active Africaneur tag at a time.

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
