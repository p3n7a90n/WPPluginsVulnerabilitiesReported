# Multiple SQL Injection in Import Module

**Severity: MEDIUM**

**Affected Product** 

Import all XML, CSV & TXT into WordPress

**Version:** 6.5.6

**Description**

SQL injection attack consists of insertion or “injection” of a SQL query via the input data from the client to the application. A successful SQL injection exploit can read sensitive data from the database, modify database data (Insert/Update/Delete), execute administration operations on the database (such as shutdown the DBMS), recover the content of a given file present on the DBMS file system and in some cases issue commands to the operating system.

**Overall CVSS v3.1 score: 6.8**

**CVSS Reference Link:** [https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator?vector=AV:N/AC:L/PR:H/UI:N/S:U/C:H/I:H/A:H/E:F/RL:W/RC:C/CR:H/IR:H/AR:H/MAV:N/MAC:L/MPR:H/MUI:N/MS:U/MC:H/MI:H/MA:H&version=3.1](https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator?vector=AV:N/AC:L/PR:H/UI:N/S:U/C:H/I:H/A:H/E:F/RL:W/RC:C/CR:H/IR:H/AR:H/MAV:N/MAC:L/MPR:H/MUI:N/MS:U/MC:H/MI:H/MA:H&version=3.1)

**Technical Impact**

Authenticated user can execute any SQL command in the database. If successfully exploited, impact could cover loss of confidentiality, loss of integrity, loss of availability, and/or loss of accountability.

**Suggested Remediation**

- Use prepared statement (with Parameterized Queries).
- Use wpdb::prepare before using wpdb::get_results

**Steps to Reproduce**

1. Install the “Ultimate CSV Importer Free” plugin by following the official installation instruction
    
    URL:  [https://wordpress.org/plugins/wp-ultimate-csv-importer/#installation](https://wordpress.org/plugins/wp-ultimate-csv-importer/#installation)
    
    ![Untitled](Multiple%20SQL%20Injection%20in%20Import%20Module%208e67b6ab9e034c8893dd1a5abada4965/Untitled.png)
    
2. You can also directly download and activate it via the Wordpress Plugins page.
    
    ![Untitled](Multiple%20SQL%20Injection%20in%20Import%20Module%208e67b6ab9e034c8893dd1a5abada4965/Untitled%201.png)
    
3. Install the Polylang Plugin and activate it as well for supporting the “Polylang Setting Fields” while importing the csv file.
    
    ![Untitled](Multiple%20SQL%20Injection%20in%20Import%20Module%208e67b6ab9e034c8893dd1a5abada4965/Untitled%202.png)
    
4. Navigate to the Import Module after activating the plugin.
    
    ![Untitled](Multiple%20SQL%20Injection%20in%20Import%20Module%208e67b6ab9e034c8893dd1a5abada4965/Untitled%203.png)
    
5. Upload any posts CSV file with language_code as one of the headers.
    
    ![Untitled](Multiple%20SQL%20Injection%20in%20Import%20Module%208e67b6ab9e034c8893dd1a5abada4965/Untitled%204.png)
    
6. On the next page, choose Import each report as ”Post” from the dropdown.
    
    ![Untitled](Multiple%20SQL%20Injection%20in%20Import%20Module%208e67b6ab9e034c8893dd1a5abada4965/Untitled%205.png)
    
7. Click on “Continue” on the next pages with the default options.
    
    ![Untitled](Multiple%20SQL%20Injection%20in%20Import%20Module%208e67b6ab9e034c8893dd1a5abada4965/Untitled%206.png)
    
8. Observe that import is completed in around 1 second in the normal scenario as shown below.
    
    ![Untitled](Multiple%20SQL%20Injection%20in%20Import%20Module%208e67b6ab9e034c8893dd1a5abada4965/Untitled%207.png)
    
9. Now, Change language_code column in the first row to
    
    Payload: “**en' union select if(user_login='wordpress',sleep(5),null) from wp_users#”**
    
    ![Untitled](Multiple%20SQL%20Injection%20in%20Import%20Module%208e67b6ab9e034c8893dd1a5abada4965/Untitled%208.png)
    
10. Save the CSV file and import the modified CSV file following the steps from 4 to 8.
11. Observe that the import took around **16 seconds** with the modified csv, as one of the user in the wordpress is of name
    
    ![Untitled](Multiple%20SQL%20Injection%20in%20Import%20Module%208e67b6ab9e034c8893dd1a5abada4965/Untitled%209.png)
    
    ![Untitled](Multiple%20SQL%20Injection%20in%20Import%20Module%208e67b6ab9e034c8893dd1a5abada4965/Untitled%2010.png)
    

## References (Vulnerable Code Flow)

1. In the **bulk_import function**, based on the deserialized key/value pair(saved when **save_fields_function** is called), different import addons file is getting called based on the switch statement. In this case it is ‘**POLYLANG’**
    
    ![                                                      *File path: SaveMapping.php#L466-469*](Multiple%20SQL%20Injection%20in%20Import%20Module%208e67b6ab9e034c8893dd1a5abada4965/Untitled%2011.png)
*File path: SaveMapping.php#L466-469*
    

2.  In the **polylang_import_function**, values from the csv column, is directly being used in the **$wpdb->get_results()** which results into multiple SQL Injection vulnerability.

    ![                                                         *File path: importExtensions/PolylangImport.php#L70-100*](Multiple%20SQL%20Injection%20in%20Import%20Module%208e67b6ab9e034c8893dd1a5abada4965/Untitled%2012.png)
*File path: importExtensions/PolylangImport.php#L70-100*
