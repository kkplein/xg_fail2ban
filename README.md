SOPHOS XG <-> FAIL2BAN

This scripts allows sophos XG with WAF proxied internal servers, to process fail2ban ban/unban requests from the internal servers.

We noticed that using the WAF, gives EXTRA security on one side, but our regular tools like fail2ban no longer work, as the webservers no longer interface directly with the external clients, but only with the XG WAF.
Therefore, fail2ban kept banning the same IPs over and over again.

We needed a solution, where the internal servers can easily block IPs on the XG using the API, and this is it.

Another advantange: as IPs are blocked on the perimeter firewall, all servers benefit from the bans immediately.

All regular fail2ban configs normally apply, like whitelists etc.

Be sure to pass the the X-Forwarded-For to the internal server.

Instructions:
* create a directory /opt/xg_fail2ban
* download and extract master.zip
* copy all zipped files to /opt/xg_fain2ban, except:
* copy xg_fail2ban.conf to /etc, and complete according to your setup

Suppose the internal server has hostname "webserver":

Then, run like:
root@webserver:/opt/xg_fail2ban# ./xg_fail2ban ban 1.2.3.4

this will generate an XML file in /tmp (and optionally also upload it to XG) that will
- first create a object def with name "f2b-webserver-1.2.3.4"
- second create a firewall rule to drop traffic coming from it 

root@webserver:/opt/xg_fail2ban# ./xg_fail2ban unban 1.2.3.4
this will generate an XML file in /tmp, (and optionally also upload it to XG) that will
- first delete the firewall rule to drop traffic coming from object f2b-webserver-1.2.3.4 
- second delete the object def with name "hostname-1.2.3.4"

Notesbbb.b

- hostname is taken from /etc/hostname, and is only used to easily identify in XG where the ban came from

- the rule is inserted in XG, after an existing rule, named "block from IPs".
You will need to create it, otherwise the script has no idea where and what group to put the rule in

- the last line in xg_fail2ban can be commented/uncommented, to actually upload the XML into XG or not.

- the rules are created (default) in DISABLED state, to make sure nothing unexpected will happen.
For production you will need "Enable" rules with action "Drop". Configure this in the conf file.

- I need to find out and document how to connect xf_fail2ban to fail2ban, so that fail2ban will use it for banning/unbanning

- I notice frequently that XG displays new rules with Any (zone) / Any (network) with is wrong, and frightening.
After a minute this usually changes automatically to the created network object. No idea why.

Troubleshoot:
- make sure to enable XG for API access from the internal server IP
- manually try to upload the XML to XG, to see return codes
- check that the last line is uncommented if nothing happens
- the script logs what it does (including the XML returned by XG) to syslog, so grep those for "xg_fail2ban"
