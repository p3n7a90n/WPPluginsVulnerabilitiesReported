# Free Pro membership of Anywhere Elementor WP Plugin

**Severity**: High

**Affected Product**

AnyWhere Elementor

**Version:** 1.2.7

**Description**

Freemius Secret Key is hardcoded in the pro.php file of AnyWhere Elementor Plugin.

**Technical Impact**

Attacker can use the secret key to buy the pro subscription for free using the test credit cards without actually paying the amount.

**Steps to Reproduce**

1. Freemius Secret key is hardcoded in the pro.php file of AnyWhere Elementor Plugin.
    
    ![Untitled](Free%20Pro%20membership%20of%20Anywhere%20Elementor%20WP%20Plugi%203fb84c8a8dcc4e5f89fe1f48069c75ba/Untitled.png)
    
    wordpress/wp-content/plugins/anywhere-elementor/includes/pro.php#L31
    
2. I uncommented the secret_key and activated the plugin to verify if the secret key is valid or not and I was able to purchase the pro membership using the test credit cards.
    
    ![Untitled](Free%20Pro%20membership%20of%20Anywhere%20Elementor%20WP%20Plugi%203fb84c8a8dcc4e5f89fe1f48069c75ba/Untitled%201.png)
    

**Vendor Contacted**

1. I reported the vulnerability to the plugin team and they have revoked the key and will delete the hardcoded key as well in the next release.
    
    ![Untitled](Free%20Pro%20membership%20of%20Anywhere%20Elementor%20WP%20Plugi%203fb84c8a8dcc4e5f89fe1f48069c75ba/Untitled%202.png)