# Nonce leak+ Any authorised user calling the endpoints

**Severity**: Medium

**Affected Product**

WPCode – Insert Headers and Footers + Custom Code Snippets – WordPress Code Manager

**Version:** 2.0.6

**Description**

Any authenticated user who has the edit permissions like **contributor, author, etc** to any one of the post can call the endpoints related to WPCode library authentication by leaking the nonce.

**Technical Impact**

Attacker can control the WPCode library authentication.

**Steps to Reproduce**

1. Install the WPCode plugin.
2. Login with any role which has the permissions to edit at least one of the post.
    
    ![Untitled](Nonce%20leak+%20Any%20authorised%20user%20calling%20the%20endpoi%209e86a31e7f214930904ffe42e20c3ad7/Untitled.png)
    
3. Open the edit page of any of the posts.
    
    ![Untitled](Nonce%20leak+%20Any%20authorised%20user%20calling%20the%20endpoi%209e86a31e7f214930904ffe42e20c3ad7/Untitled%201.png)
    
4. Observe the response of this page for the wp_admin nonce.
    
    ![Untitled](Nonce%20leak+%20Any%20authorised%20user%20calling%20the%20endpoi%209e86a31e7f214930904ffe42e20c3ad7/Untitled%202.png)
    
5. Now using the above nonce, user can call any of the endpoints that relies on the **wp_admin nonce check** and does not have any authorisation check.
6. User can call any of the endpoints related to the WPCode library authentication such as start_auth, delete_auth, store auth key in the database.
    
    ![Untitled](Nonce%20leak+%20Any%20authorised%20user%20calling%20the%20endpoi%209e86a31e7f214930904ffe42e20c3ad7/Untitled%203.png)
    
    ![Start the auth](Nonce%20leak+%20Any%20authorised%20user%20calling%20the%20endpoi%209e86a31e7f214930904ffe42e20c3ad7/Untitled%204.png)
    
    Start the auth
    
    ![Save the auth key and then load all the snippets from the library using the key(Key used above is not correct and its just for the POC)](Nonce%20leak+%20Any%20authorised%20user%20calling%20the%20endpoi%209e86a31e7f214930904ffe42e20c3ad7/Untitled%205.png)
    
    Save the auth key and then load all the snippets from the library using the key(Key used above is not correct and its just for the POC)
    
    This can be abused in the Pro version if the attacker saves malicious code snippets and after linking the attacker account if any of the users uses the attacker’s snippet.
    
    ![Untitled](Nonce%20leak+%20Any%20authorised%20user%20calling%20the%20endpoi%209e86a31e7f214930904ffe42e20c3ad7/Untitled%206.png)
    
    ![Delete the cache data stored in the files and deauthenticate the library api](Nonce%20leak+%20Any%20authorised%20user%20calling%20the%20endpoi%209e86a31e7f214930904ffe42e20c3ad7/Untitled%207.png)
    
    Delete the cache data stored in the files and deauthenticate the library api
    

Vulnerable Code Snippets

1. WPCode library related endpoints.
    
    ![Untitled](Nonce%20leak+%20Any%20authorised%20user%20calling%20the%20endpoi%209e86a31e7f214930904ffe42e20c3ad7/Untitled%208.png)
    
    ![Untitled](Nonce%20leak+%20Any%20authorised%20user%20calling%20the%20endpoi%209e86a31e7f214930904ffe42e20c3ad7/Untitled%209.png)
    
    wp-content/plugins/insert-headers-and-footers/includes/class-wpcode-library-auth.php
    
    1. Code related to generating nonce
        
        ![Untitled](Nonce%20leak+%20Any%20authorised%20user%20calling%20the%20endpoi%209e86a31e7f214930904ffe42e20c3ad7/Untitled%2010.png)
        
    
    wp-content/plugins/insert-headers-and-footers/includes/admin/admin-scripts.php#L82-91