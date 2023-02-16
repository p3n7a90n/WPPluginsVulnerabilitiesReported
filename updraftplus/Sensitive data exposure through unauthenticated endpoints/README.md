# Sensitive data exposure through unauthenticated endpoints

**Severity**: Low

**Affected Product**

UpdraftPlus WordPress Backup Plugin

**Version:** 1.22.24

**Description**

Plugin exposes the unauthenticated endpoints  via “wp_ajax_nopriv_$action” hook that can be used to extract the sensitive information and restore the backup as well if the nonce is known.

**Overall CVSS v3.1 score**: 5.8

**CVSS Reference Link:** [https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator?vector=AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H/E:F/RL:W/RC:C/CR:H/IR:H/AR:H/MAV:N/MAC:L/MPR:L/MUI:N/MS:U/MC:H/MI:H/MA:H&version=3.1](https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator?vector=AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N/E:F/RL:W/RC:C/CR:H/IR:H/AR:H/MAV:N/MAC:L/MPR:N/MUI:N/MS:U/MC:L/MI:N/MA:N&version=3.1)

**Technical Impact**

Attacker can view the content of the generated log file and can restore the backup data as well if the nonce is leaked in the “wp-content/updraft” directory.

**Steps to Reproduce**

1. Install the updraft plugin.
    
    [https://wordpress.org/plugins/updraftplus/](https://wordpress.org/plugins/updraftplus/)
    
2. Make a unauthenticated POST request call to “wp-admin/admin-ajax.php” with the `action=updraft_ajaxrestore&updraftplus_ajax_restore=start_ajax_restore` body and observe the nonce in the response.
    
    ![Untitled](Sensitive%20data%20exposure%20through%20unauthenticated%20en%20b4892f0d811f498588f7a64582d7bbdb/Untitled.png)
    
3. Hit the below endpoint with the nonce from the previous response and observe the content of the log file
    
    [http://localhost:8000/payatu/wordpress/wp-content/updraft/log.<nonce>.txt](http://localhost:8000/payatu/wordpress/wp-content/updraft/log.f5ac049dca02.txt)
    
    ![Untitled](Sensitive%20data%20exposure%20through%20unauthenticated%20en%20b4892f0d811f498588f7a64582d7bbdb/Untitled%201.png)
    

Note:
Attacker can restore the content of the backup as well if the nonce is known.

![Untitled](Sensitive%20data%20exposure%20through%20unauthenticated%20en%20b4892f0d811f498588f7a64582d7bbdb/Untitled%202.png)

Vulnerable Code Snippet:

![Untitled](Sensitive%20data%20exposure%20through%20unauthenticated%20en%20b4892f0d811f498588f7a64582d7bbdb/Untitled%203.png)

wp-content/plugins/updraftplus/admin.php#L525-527