# Code Injection in WP ALL Export (Non-Admin)

**Severity:** High

**Affected Product**

WP ALL Export (All Export Pro Plugin for WordPress)

**Version:** 1.7.8

**Description**

The term "code injection" refers to various attack types where system code is introduced and then interpreted or executed by the application. Attacks of this kind prey on careless handling of unreliable data. The absence of adequate input/output data validation frequently makes these kinds of attacks possible.

User can input any default or user-defined function that will get executed in the system.

**Overall CVSS v3.1 score**: 8.3

**CVSS Reference Link:** [https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator?vector=AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H/E:F/RL:W/RC:C/CR:H/IR:H/AR:H/MAV:N/MAC:L/MPR:L/MUI:N/MS:U/MC:H/MI:H/MA:H&version=3.11](https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator?vector=AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H/E:F/RL:W/RC:C/CR:H/IR:H/AR:H/MAV:N/MAC:L/MPR:L/MUI:N/MS:U/MC:H/MI:H/MA:H&version=3.11)

**Technical Impact**

Attacker can execute any command on the system that leads to complete compromise of the system. If successfully exploited, impact could cover loss of confidentiality, loss of integrity, loss of availability, and/or loss of accountability.

**Suggested Remediation**

- Implement proper authorization check to ensure the required fields are updated by the admin role only.

**Steps to Reproduce**

1. Install the “WP All Export Pro” plugin after buying and following the official installation instruction
    
    **URL**:  [https://wordpress.org/plugins/wp-all-export/#installation](https://wordpress.org/plugins/wp-all-export/#installation)
    
2. Click on the “New Export” and select Posts from the drop-down menu for Specific Post Type
    
    ![Untitled](Code%20Injection%20in%20WP%20ALL%20Export%20(Non-Admin)%206a840667a0ae4665b640361ba024c55f/Untitled.png)
    
3. Keep the default option on the next page and select “Continue”
    
    ![Untitled](Code%20Injection%20in%20WP%20ALL%20Export%20(Non-Admin)%206a840667a0ae4665b640361ba024c55f/Untitled%201.png)
    
4. Select the checkbox of “Allow non-admins to run into the export in Client Mode” to give the access to the non-admin users
    
    ![Untitled](Code%20Injection%20in%20WP%20ALL%20Export%20(Non-Admin)%206a840667a0ae4665b640361ba024c55f/Untitled%202.png)
    
5. Export is completed successfully.
    
    ![Untitled](Code%20Injection%20in%20WP%20ALL%20Export%20(Non-Admin)%206a840667a0ae4665b640361ba024c55f/Untitled%203.png)
    
6. Open the Settings and give any of the users the access of the exports (In this case, author role is selected)
    
    ![Untitled](Code%20Injection%20in%20WP%20ALL%20Export%20(Non-Admin)%206a840667a0ae4665b640361ba024c55f/Untitled%204.png)
    
7. Now, logout from the admin account and login into the author role account
    
    ![Untitled](Code%20Injection%20in%20WP%20ALL%20Export%20(Non-Admin)%206a840667a0ae4665b640361ba024c55f/Untitled%205.png)
    
8. User (Non-Admin) will be able to see all the export for which admin has given the permissions.
    
    ![Untitled](Code%20Injection%20in%20WP%20ALL%20Export%20(Non-Admin)%206a840667a0ae4665b640361ba024c55f/Untitled%206.png)
    
9. Click on “Run Export” for any of the export
    
    ![Untitled](Code%20Injection%20in%20WP%20ALL%20Export%20(Non-Admin)%206a840667a0ae4665b640361ba024c55f/Untitled%207.png)
    
10. Click on the “Confirm & Run Export” and intercept this request into any of the proxy tool like burpsuite.
    
    Request path: /wordpress/wp-admin/admin.php?page=pmxe-admin-manage&id=32&action=update
    
    ![Untitled](Code%20Injection%20in%20WP%20ALL%20Export%20(Non-Admin)%206a840667a0ae4665b640361ba024c55f/Untitled%208.png)
    

11.  Add the below fields and turn the intercept off

```jsx
------WebKitFormBoundaryebX838kmAzPMht5c
Content-Disposition: form-data; name="cc_type[]"
sql
------WebKitFormBoundaryebX838kmAzPMht5c
Content-Disposition: form-data; name="cc_code[]"
system('id')
------WebKitFormBoundaryebX838kmAzPMht5c
Content-Disposition: form-data; name="cc_php[]"
test
------WebKitFormBoundaryebX838kmAzPMht5c
Content-Disposition: form-data; name="cc_sql[]"
test
```

![Untitled](Code%20Injection%20in%20WP%20ALL%20Export%20(Non-Admin)%206a840667a0ae4665b640361ba024c55f/Untitled%209.png)

1. Export is completed. Now click on the “DownloadCSV” to see the content.
    
    ![Untitled](Code%20Injection%20in%20WP%20ALL%20Export%20(Non-Admin)%206a840667a0ae4665b640361ba024c55f/Untitled%2010.png)
    
2. Observe that **payload(system(‘id’**)) got executed and the output in the CSV which was inserted in step 11
    
    ![Untitled](Code%20Injection%20in%20WP%20ALL%20Export%20(Non-Admin)%206a840667a0ae4665b640361ba024c55f/Untitled%2011.png)
    

*Note:  Attacker can exploit the same flow by selecting another Post Type like Comments, Taxonomies.*

**References**

- **Vulnerable Code Snippets**
    1. **cc_type** value from the post request is getting used in the switch statement and in case of **‘sql’**, **cc_code($fieldCode) value(payload)** is being used into **the eval function (Sink)**
        
        ![*File path: libraries/XmlExportCpt.php#L498-513*](Code%20Injection%20in%20WP%20ALL%20Export%20(Non-Admin)%206a840667a0ae4665b640361ba024c55f/Untitled%2012.png)
        
        *File path: libraries/XmlExportCpt.php#L498-513*
        
        *Note: Same vulnerable code flow is available in other Post Types like Comments, Taxonomies.*
        
    2. Post Type: Taxonomy
        
        ![*File path: libraries/XmlExportTaxonomy.php#L370-390*](Code%20Injection%20in%20WP%20ALL%20Export%20(Non-Admin)%206a840667a0ae4665b640361ba024c55f/Untitled%2013.png)
        
        *File path: libraries/XmlExportTaxonomy.php#L370-390*
        
    3. Post Type: Comment
        
        ![*File path: libraries/XmlExportComment.php#L340-354*](Code%20Injection%20in%20WP%20ALL%20Export%20(Non-Admin)%206a840667a0ae4665b640361ba024c55f/Untitled%2014.png)
        
        *File path: libraries/XmlExportComment.php#L340-354*
        
    
    **Links**
    
    - **Burpsuite Tool:** [https://portswigger.net/burp](https://portswigger.net/burp)