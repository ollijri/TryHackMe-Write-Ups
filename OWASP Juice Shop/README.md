## Open for business!

<details> 
  <summary> Gaining Access</summary>
  <p></p>
  Use either openVPN or the hackbox, start the virtual machine and then enter the IP address into the URL bar at the top of your browser.
  <p></p>
  
  
  ![image](https://user-images.githubusercontent.com/66912443/184948233-78f2a0d2-ce02-4d9d-ac17-61d77c622098.png)
</details>

## Let's go on an adventure!

<details> 
  <summary> Question #1: What's the Administrator's email address? </summary>
  <p></p>
  An administrator has left a review on a product logged in as the admin which exposes what the address is
  <p></p>
  
  ![image](https://user-images.githubusercontent.com/66912443/184948637-48d4e7eb-fdf7-4f5a-bbdf-753cf4a78ec0.png)
  
</details>

<details> 
  <summary> Question #2: What parameter is used for searching? </summary>
  <p></p>
  By using the search function (magnifying glass) we can easily expose this parameter
  <p></p>
  
  ![image](https://user-images.githubusercontent.com/66912443/184950365-ea52ef96-0cdf-402b-a876-f412842e855a.png)
  
  <p></p>
  
  Once anything has been searched, from the URL bar we can see the effect of the search, exposing the parameter that is used before the input
  
  ![image](https://user-images.githubusercontent.com/66912443/184950178-7598108e-ec73-4ddb-ab66-6164f2da31aa.png)

</details>

<details> 
  <summary> Question #3: What show does Jim reference in his review? </summary>
   <p></p>
   By looking around the products you can find "jim@juice-sh.op" left a review on the green smoothie.
   <p></p>
  
   ![image](https://user-images.githubusercontent.com/66912443/184951521-d0689dd5-a76c-4f9f-a3b8-4d3fc3ffd71f.png)
   
   A simple google search of "replicator" reveals the answer
   <p></p>
   
</details>

## Inject the juice

<details> 
  <summary> Question #1: Log into the administrator account! </summary>
  <p></p>
  At this point make sure that intercept is on in burpsuite and if using foxyproxy that the option is set to "Burp"
  <p></p>
  
  ![image](https://user-images.githubusercontent.com/66912443/184953030-12b1dcc6-10bd-4aeb-bff9-af4fd9e320c4.png)
  
  <p></p>
  
  ![image](https://user-images.githubusercontent.com/66912443/184953343-2a97460f-6b6e-47a5-8b86-462183be51b8.png)
  
  Now that Burpsuite is up, navigate to "account" (next to the search bar) and enter in anything into the email and password field 
  
  ![image](https://user-images.githubusercontent.com/66912443/184953840-c30265c7-86ee-4cf1-8002-c40a4e967a5e.png)
  
  It may take a couple of goes of forwarding but back in burpsuite you should see that the email and password is sent off in plaintext to the SQL server which makes it easily susceptible to change.
  
  ![image](https://user-images.githubusercontent.com/66912443/184955212-90e6ac93-072d-4099-9eb1-7aacb6eb2a4e.png)
  
  To exploit this, we change the email parameter to "' or 1=1--".
  
  ![image](https://user-images.githubusercontent.com/66912443/184957695-c2a55d78-5f07-4f71-8f3e-7e2bd745f77d.png)

  The first character, the apostrophe, closes the brackets in the initial SQL query that is sent to the server. This is then followed by a second command in the same syntax: "OR" which using logic gates as an example will output true if either side of its input is true which "1=1" will always be, therefore tricking the system into always making the email valid. Finally, the double dashes at the end indicate the start of the comment meaning everything after this initial code will be ignored or "commented out". 
  
  As no email was given, SQL has defaulted to the top user id "0". In a lot of databases, the first user to be created is usually the administrator, as is the case in this challenge as well thus granting us admin access.
  
  ![image](https://user-images.githubusercontent.com/66912443/184960236-c5f5c13b-4728-465c-9565-20bf15417c98.png)
  
</details>


<details> 
  <summary> Question #2: Log into the Bender account! </summary>
   <p><p/>
  Following the exact same method used to get into the admin account, supplement "' or 1=1--" in the email field with "bender@juice-sh.op'--" instead.
  
  You may have noticed that the apostrophe and double dashes have moved next to each other. As this is an actual email, everything after it is commented out instead as calling back to the previous question, the apostrophe marks the closing of brackets to an SQL query.
  
  ![image](https://user-images.githubusercontent.com/66912443/184964819-9958b99b-ccbd-4ba1-8546-c871cdc1c9cf.png)
  
</details>

## Who broke my lock?! 

<details> 
  <summary> Question #1: Bruteforce the Administrator account's password! </summary>
   <p><p/>
   First, just like the two times previously, intercept the random information inputted into the account login page, as we know the admin email we might as well use that as one of the fields to prevent having to write it out again
   
   ![image](https://user-images.githubusercontent.com/66912443/185338620-eb019681-8ee0-46c0-9682-4996486b3cd0.png)
   
   Next, for the purpose of brute forcing, right click in the proxy window where the text is (anywhere will do) and select "send to intruder". Once on the intruder window hit the "Clear §" button to the right hand side to remove the weird symbol that burp uses for quotations.
   
   Once that is done, remove whatever you put into the password field (excluding the quotation marks) and replace it with two of those symbols using the "add §" button. This symbol indicates what burp needs to change when bruteforcing.
   
   ![image](https://user-images.githubusercontent.com/66912443/185339578-be1ec92c-cbc9-42bd-aa3e-832bfae87c94.png)
   
   Next up is selecting a payload. Navigate to the next subtab "payloads" and add/load a new payload under "payload options" and start the attack. For this example a list from "seclists" is being used. These are excellent collections of lists similar to rockyou full of usernames, passwords, URLs etc.
  
  ![image](https://user-images.githubusercontent.com/66912443/185341240-724a669f-2b4a-428e-8b5e-73874dbb831c.png)

  This open a window and start to tick along, its best to sort by status code - "2xx = [success]". Depending on the speed of your computer/VM this may take a while. Once its completed and you have the password (the status code 200 is a dead giveaway) you can log in and get the answer!
   
</details>

<details>
<summary> Question #2: Reset Jim's password! </summary>
<p><p/>
When it comes to this task, its heavily based on "Open Source Intelligence" (OSINT) and provides a perfect example of the kind of ways to gather information about someone in order to break into their account. 
  
  To begin, navigate to the forgot password screen and enter Jim's email (obtained in an earlier section). Here you will see the security question "Your eldest siblings middle name"
  
![image](https://user-images.githubusercontent.com/66912443/185348966-d0a5a076-a6f9-4268-9473-5aa544e5bcd0.png)
  
  For this, external research is required. Just like how in a real world exaple you'd need to scour the targets social media to uncover information, in this instanced its star trek themed so wikipedia is required (to be honest, without tryhackme pointing it out I would not have had a CLUE, its so vague!).
  
  According to the wiki, James (Jim) Kirk had one sibling, "George Samuel Kirk".
  
  ![image](https://user-images.githubusercontent.com/66912443/185391944-6925eb26-d0f6-483b-b738-b123828963d5.png)
  
  After entering the middle name of his sibling into the security question box, the password was successfully changeable!
  
  ![image](https://user-images.githubusercontent.com/66912443/185392395-089a483d-9c21-47b2-a87b-00021255de27.png)

</details>

## AH! Don't look!

<details> 
  <summary> Question #1: Access the Confidential Document! </summary>
  <p></p>
  In the "About us" section is a link to auto-download a file... But what is the full link? Where is the file stored?
  <p><p/>
  
  
  ![image](https://user-images.githubusercontent.com/66912443/185393977-a6b703ac-c954-4eb4-974e-973e70830270.png)
  
  By copying the link location and pasting it into a new tab, it reveals the full path as the following:
  
  ``` http://10.10.255.126/ftp/legal.md ```
  
  This exposes the existance of an ftp server which could be insecure. By removing the name of the file and stopping at /ftp/ you get access!
  
  ![image](https://user-images.githubusercontent.com/66912443/185394599-2aaa5c57-c39f-47ee-932d-ce6d1458df7e.png)
  
  Download and read aquisitions.mb. This is the confidential document and completes the challenge, heading back to the website will reveal the code.
  
</details>

<details> 
  <summary> Question #2: Log into MC SafeSearch's account! </summary>

  <p><p/>
  
  https://www.youtube.com/watch?v=v59CX2DiX0Y
  
</details>

<details> 
  <summary> Question #3: Download the Backup file! </summary>
  <p></p>
  Returning to /ftp/, trying to download 'package.json.bak' will throw the following error:
  <p><p/>
  
  ![image](https://user-images.githubusercontent.com/66912443/185403915-67eb460c-6013-4c62-821b-f60cbad4e077.png)
  
  If the code behind the scenes isnt sanitising the input correctly, a 'null byte' can be used to terminate the end of a string and therefore potentially blocking code after the URL that would tell the system not to let you download a file. 
  
  A null byte typically looks like "%00" but for use within URLs it looks like "%2500". Putting ".md" after the null byte completes the exploit and lets you download. As there is techincally not a "package.json.bak.md" you cant just put the extension there and has to be after the null byte.
  <p><p/>
  
  ![image](https://user-images.githubusercontent.com/66912443/185405805-098139ff-1c21-4412-8e94-ff028dd6b1ea.png)
  
  
  Returning to the main menu gives you the answer.
  <p></p>
  
</details>

## Who's flying this thing? 

## Where did that come from?

## Exploration

7efd3174f9dd5baa03a7882027f2824d2f72d86e
