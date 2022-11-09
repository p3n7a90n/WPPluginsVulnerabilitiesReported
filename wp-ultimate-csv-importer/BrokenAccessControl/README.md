# Broken Authorization

**Severity:** HIGH

**Affected Product**

Import all XML, CSV & TXT into WordPress

**Version:** 6.5.6

**Description**

Authors/Editors can access the plugin without having the proper privilege given by the admin.

Any Authenticated User can also access the plugin if the securitykey/nonce is known.

**Overall CVSS v3.1 score: 8.3**

**CVSS Reference Link:** [https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator?vector=AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H/E:F/RL:W/RC:C/CR:H/IR:H/AR:H/MAV:N/MAC:L/MPR:L/MUI:N/MS:U/MC:H/MI:H/MA:H&version=3.1](https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator?vector=AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H/E:F/RL:W/RC:C/CR:H/IR:H/AR:H/MAV:N/MAC:L/MPR:L/MUI:N/MS:U/MC:H/MI:H/MA:H&version=3.1)

**Technical Impact**

Authenticated user can access the plugin functionality and will be able to call all the ajax request.

**Suggested Remediation**

- Proper authorization check should be implemented to check that logged in user has the required privileges.
- Add a check in the **onpluginsload function based on the logged in request** before including the files.

**Steps to Reproduce**

1. Install the “Ultimate CSV Importer Free” plugin by following the official installation instruction
    
    URL:  [https://wordpress.org/plugins/wp-ultimate-csv-importer/#installation](https://wordpress.org/plugins/wp-ultimate-csv-importer/#installation)
    
    ![Untitled](Broken%20Authorization%20ab22b31c1fb949bdba2a31e152ccf6a4/Untitled.png)
    
2. You can also directly download and activate it via the Wordpress Plugins page
    
    ![Untitled](Broken%20Authorization%20ab22b31c1fb949bdba2a31e152ccf6a4/Untitled%201.png)
    
3. Navigate to the Settings Module after activating the plugin and give authors/editors to access the plugin for once.
    
    ![Untitled](Broken%20Authorization%20ab22b31c1fb949bdba2a31e152ccf6a4/Untitled%202.png)
    
4. Open the WordPress in another window and login as author or editor.
    
    You will be able to view the plugin.
    
    ![Untitled](Broken%20Authorization%20ab22b31c1fb949bdba2a31e152ccf6a4/Untitled%203.png)
    
5. Click on the Plugin and save the AJAX request to capture the author cookie and nonce(securitykey).
    
    ![Untitled](Broken%20Authorization%20ab22b31c1fb949bdba2a31e152ccf6a4/Untitled%204.png)
    
6. Now, go to another window where admin account is logged in and disable the access for authors/editors and save the ajax request.
    
    ![Untitled](Broken%20Authorization%20ab22b31c1fb949bdba2a31e152ccf6a4/Untitled%205.png)
    
    ![Untitled](Broken%20Authorization%20ab22b31c1fb949bdba2a31e152ccf6a4/Untitled%206.png)
    
7. Now the author won’t have the access to the plugin after refreshing the page.

![Untitled](Broken%20Authorization%20ab22b31c1fb949bdba2a31e152ccf6a4/Untitled%207.png)

1. Modify the request captured in
    
    Replace the **author cookie and the securekey/nonce** with **the values captured for author in Step 5.**
    
    Turn the **“author_editor_access” to true** as shown below.
    
    Send the request and it results in “success”.
    
    ![Untitled](Broken%20Authorization%20ab22b31c1fb949bdba2a31e152ccf6a4/Untitled%208.png)
    
2. Now, Refresh the authors account and you will be able to see the plugin again without admin giving the access to the plugin.
    
    ![Untitled](Broken%20Authorization%20ab22b31c1fb949bdba2a31e152ccf6a4/Untitled%209.png)
    

***Note: Any authenticated user can make the AJAX call if the user is having the proper secure_key/nonce as there is no proper check for the authorization.***

## References (Vulnerable Code Flow)

1. In the **onplusingload function,** based on the page and action, different hooks are being called.
    
    ![*File path: wp-ultimate-csv-importer.php#L447-453*](Broken%20Authorization%20ab22b31c1fb949bdba2a31e152ccf6a4/Untitled%2010.png)
    
    *File path: wp-ultimate-csv-importer.php#L447-453*
    
2. There is one check in the wp-csv-hooks function based on the “edit_published_posts” but this can be easily bypassed (OR condition in the if statement) by mentioning the page in the request to “com.smackcoders.csvimportnew.menu”
    
    ![*File path: wp-csv-hooks.php#L11-14*](Broken%20Authorization%20ab22b31c1fb949bdba2a31e152ccf6a4/Untitled%2011.png)
    
    *File path: wp-csv-hooks.php#L11-14*
    
3.  Only nonce check is there in every AJAX call via “check_ajax_referer” function.
    
    ![Untitled](Broken%20Authorization%20ab22b31c1fb949bdba2a31e152ccf6a4/Untitled%2012.png)