# Admin + Sqli in Replace URL Module

**Affected Product**

Elementor Website Builder Wordpress plugin

**Version:** 3.10.0

**Severity**: Medium

**CVSS v3 Score:** 6.6:[AV:N/AC:L/PR:H/UI:N/S:U/C:N/I:H/A:L/E:F/RL:W/RC:C/CR:H/IR:H/AR:H/MAV:N/MAC:L/MPR:H/MUI:N/MS:U/MC:N/MI:H/MA:L](https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator?vector=AV:N/AC:L/PR:H/UI:N/S:U/C:N/I:H/A:L/E:F/RL:W/RC:C/CR:H/IR:H/AR:H/MAV:N/MAC:L/MPR:H/MUI:N/MS:U/MC:N/MI:H/MA:L&version=3.1)

**Description**

Replace URL in Tools module is vulnerable to SQL Injection. 

**Steps to Reproduce**

1. Install the Elementor Free plugin.
2. Login with the admin role and activate the plugin.
3. Navigate to the tools section in the plugin.

![Untitled](Admin%20+%20Sqli%20in%20Replace%20URL%20Module%20cc5a70dd1fb94c38b93dd0e604ddb343/Untitled.png)

1. Before the injection, meta_key of the meta_id 99999 is key3

![Untitled](Admin%20+%20Sqli%20in%20Replace%20URL%20Module%20cc5a70dd1fb94c38b93dd0e604ddb343/Untitled%201.png)

1. Navigate to “Replace URL” section and insert the SQLi payload in the “new-uri” field.

Payload: [`http://localhost:8000/?test'),meta_key='key4'where+meta_id=99999;#`](http://localhost:8000/?test%27),meta_key=%27key4%27where+meta_id=99999;#)

Above payload will update the meta_key to key4 of meta_id=999999

![Untitled](Admin%20+%20Sqli%20in%20Replace%20URL%20Module%20cc5a70dd1fb94c38b93dd0e604ddb343/Untitled%202.png)

1. Click on the Replace URL and Observe the popup “One Row Updated”. we can confirm the same in the database as well.

![Untitled](Admin%20+%20Sqli%20in%20Replace%20URL%20Module%20cc5a70dd1fb94c38b93dd0e604ddb343/Untitled%203.png)

**Vulnerable Code Snippets:**

1. URL is directly being used in the query statement after validating it with “filter_var( $from, FILTER_VALIDATE_URL )” which can be bypassed.
    
    ![Untitled](Admin%20+%20Sqli%20in%20Replace%20URL%20Module%20cc5a70dd1fb94c38b93dd0e604ddb343/Untitled%204.png)
    
    wp-content/plugins/elementor/includes/utils.php#L172-184