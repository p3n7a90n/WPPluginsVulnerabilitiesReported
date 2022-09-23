# Unauthenticated File upload Size limit bypass

**Severity:** Medium

**Affected Product**

Drag and Drop Multiple File Upload – Contact Form 7

**Version:** 1.3.6.4

**Description**

Attacker can control the file length limit and can bypass the limit set by the developer in the contact form.

**Overall CVSS v3.1 score:** 4.4

**CVSS Reference Link:** [https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator?vector=AV:N/AC:L/PR:N/UI:N/S:U/C:N/I:N/A:L/E:F/RL:W/RC:C/CR:X/IR:X/AR:L/MAV:N/MAC:L/MPR:N/MUI:N/MS:U/MC:N/MI:N/MA:L&version=3.1](https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator?vector=AV:N/AC:L/PR:N/UI:N/S:U/C:N/I:N/A:L/E:F/RL:W/RC:C/CR:X/IR:X/AR:L/MAV:N/MAC:L/MPR:N/MUI:N/MS:U/MC:N/MI:N/MA:L&version=3.1)

**Technical Impact**

Attacker can upload large files which could impact the availability of the system based on the configuration and can lead to DOS.

**Suggested Remediation**

- Implement proper check on the file upload size with the value set by the developer in the contact form.

**Steps to Reproduce**

1. Install the “Drag and Drop Multiple File Upload” plugin following the official installation instruction
    
    **URL:**  [https://wordpress.org/plugins/drag-and-drop-multiple-file-upload-contact-form-7/#installation](https://wordpress.org/plugins/drag-and-drop-multiple-file-upload-contact-form-7/#installation)
    
2. Click on the “Drag and Drop Upload” in the contact section
    
    ![Untitled](Unauthenticated%20File%20upload%20Size%20limit%20bypass%20c0e66adc0f904c9b967969783e4d32b9/Untitled.png)
    
3. Click on the “multiple file upload” option and set the File Size limit to “204800” (200 KB) and insert the tag.
    
    ![Untitled](Unauthenticated%20File%20upload%20Size%20limit%20bypass%20c0e66adc0f904c9b967969783e4d32b9/Untitled%201.png)
    
4. Copy the shortcode and use it in any post page.
    
    ![Untitled](Unauthenticated%20File%20upload%20Size%20limit%20bypass%20c0e66adc0f904c9b967969783e4d32b9/Untitled%202.png)
    
5. After using it in the post, Upload the file of any length using curl and **set the size_limit to “1048576”(1 MB)** accordingly based on the upload file size as shown below.
    
    ```bash
    curl -X POST -F "size_limit=10485760" -F "action=dnd_codedropz_upload" -F "type=click" -F "form_id=156" -F "upload_name=upload-file-235" -F "upload-file=@upload.png"  http://localhost:8000/wordpress/wp-admin/admin-ajax.php
    ```
    
    ![Untitled](Unauthenticated%20File%20upload%20Size%20limit%20bypass%20c0e66adc0f904c9b967969783e4d32b9/Untitled%203.png)
    
6. Observe that the file got upload successfully from the response and upload file directory.
    
    ![Untitled](Unauthenticated%20File%20upload%20Size%20limit%20bypass%20c0e66adc0f904c9b967969783e4d32b9/Untitled%204.png)
    
    ![Untitled](Unauthenticated%20File%20upload%20Size%20limit%20bypass%20c0e66adc0f904c9b967969783e4d32b9/Untitled%205.png)
    

**References**

- Vulnerable Code Snippets
    1. File size limit validation is based on the “size_limit” value controlled by the attacker.
        
        ![lib\session-file-helpers.js#L783](Unauthenticated%20File%20upload%20Size%20limit%20bypass%20c0e66adc0f904c9b967969783e4d32b9/Untitled%206.png)
        
        lib\session-file-helpers.js#L783