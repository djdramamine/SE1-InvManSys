<h3> Default User Accounts </h3>
  * Nathan - npadilla<br>
  * Alaina - asmith<br>
  * Clay   - cflannery<br>
  * Justin - jjarosz<br>
  * Cameron - cthatcher
<br>
  <h3> Default User Passwords </h3>
  <i>password</i>
 <br><br>
 <i> Note: You do NOT have sudo permissions... <br>
 This is my own hardware please avoid from taking advantage of any security flaws inherent to Pi's
  <hr>
<h3> Connecting to the Linux Server </h3>
I have enabled remote SSH and whitelisted only the user accounts I've created today. The server will be up 24/7, but
I'm not sure how long the domain will last (it was free)
<br><br>
SSH using your favorite Terminal client on the default port (22) using your username and the default password
<br><br>
<h3> Connecting to the Database </h3>
I've enabled remote access into the database to your accounts on the default port (3306)<br><br>
Using MySQL Workbench create a new  standard connection with the following information:<br><br>
<b>Hostname:</b> se1.jumpingcrab.com <b>Port:</b> 3306<br>
<b>Username:</b> <i>Your username </i><br>
<b>Click Store in Vault ...</b> to store password (It will prompt)<br>
<b>Default Schema:</b> <i>InvManSys</i><br><br>
At this point you can click <i>Test Connection</i> or <i>OK<i/><br>
<b>Click Continue Anyway</b> to log into the database
<hr>
<h3> Changing Default Password </h3>
<h4> Changing Password for Linux Account </h4>
After logging in to the linux server over ssh you should attempt to change your password.<br>
This can be done with the <i>passwd</i> command
<br><br>
<h4> Changing Password for MySQL Account </h4>
<h5> Over SSH </h5>
Log into the server using SSH
Type <b> mysql -p </b><br>
Supply your current MySQL Password<br>
Type <b> SET PASSWORD = PASSWORD('</b><i>new_password</i><b>');</b><br><br>
<h5>Over MySQL Workbench</h5>
Connect to the database by clicking on the connection made earlier<br>
Type <b> SET PASSWORD = PASSWORD('</b><i>new_password</i><b>');</b><br>
Run the Query<br>
<br>
<hr>
<i>Let me know if there are any issues with any of this.</i>
