# Unauthenticated + User enumeration

**Severity**: Medium

**Overall CVSS Score:** 5.8:[AV:P/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N/E:F/RL:W/RC:C/CR:H/IR:H/AR:H/MAV:N/MAC:L/MPR:N/MUI:N/MS:U/MC:L/MI:X/MA:X](https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator?vector=AV:P/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N/E:F/RL:W/RC:C/CR:H/IR:H/AR:H/MAV:N/MAC:L/MPR:N/MUI:N/MS:U/MC:L/MI:X/MA:X&version=3.1) 

**Affected Product**

Essential Addons for Elementor

**Version:** 5.5.2

**Description**

Unauthenticated User can enumerate the users present in the wordpress database.

**Technical Impact**

User can enumerate the users even if the username is blocked via different methods.

User can even enumerate the users even the user does not have the access to read a user unlike the wp-users rest endpoint.  

**Steps to Reproduce**

1. Install the Essential Addons for Elementor Plugin
2. Call the admin-ajax.php endpoint with the below POST body
    
    `action=eael_select2_get_title&id[]=2&id[]=3&source_name=user`
    
    ![Untitled](Unauthenticated%20+%20User%20enumeration%2070b321d07f4149c5863a12f538980173/Untitled.png)
    
3. User can iterate through the different user using the id parameter.

**Vulnerable Code Snippet**

1. “select2_ajax_get_posts_value_titles” function is reponsible for sending the data using the “eael_select2_get_title” unauthenticated and authenticated hook.
    
    ![Untitled](Unauthenticated%20+%20User%20enumeration%2070b321d07f4149c5863a12f538980173/Untitled%201.png)
    
    ![Untitled](Unauthenticated%20+%20User%20enumeration%2070b321d07f4149c5863a12f538980173/Untitled%202.png)