# To have an API with cleaner URLs, we use an .htaccess file with url rewriting :

![image.png](/.attachments/image-cc64e087-03c9-4c2c-ac07-fb7ee9d6bc9c.png)

`Options +FollowSymlinks` allows server to follow the symbolic links.
`RewriteEngine on` activate the url rewrite module.
`RewriteCond %{REQUEST_FILENAME} !-f` check whether the requested page exists or not and if it is a regular file.
`RewriteCond %{REQUEST_FILENAME} !-d` check whether the requested page exists or not and if it is a folder.

`RewriteRule` is a specific keyword for the mod_rewrite module that indicates that the line defines a rewrite rule.
It work like this : `RewriteRule URL_TO_REWRITE REWRITTEN_URL` and use regular expressions.

## Regular expressions :

**^** : indicates the beginning of the URL to rewrite. This character is optional but it is more rigorous to use it.
**$** : indicates the end of the URL to rewrite. This character is optional but it is more rigorous to use it.
**()** : the parentheses are used to enclose a variable whose value is retrieved in the rewritten url (gid = $ 1 indicates that the variable named gid will take the value located in the first pair of parentheses, $1 is the first variable, $2 the second, etc.).
**[a-zA-Z0-9]*** : indicates that the variable is composed of zero, one or more digits and letters.
**[L]** : flag (option) means "Last", telling the rewrite module to stop.





