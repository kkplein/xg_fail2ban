* create a directory /opt/xg_fail2ban
* copy all files to it, except:
* copy xg_fail2ban.conf to /etc, and complete according to your setup

Then, run like:
 /opt/xg_fail2ban/xg_fail2ban
to see instructions, namely:

  xg_fail2ban ban 1.2.3.4
this will generate an XML file in /tmp, that you can upload via the API that will
- first create a object def with name "hostname-1.2.3.4"
- second create a firewall rule to drop traffic coming from it 

  xg_fail2ban unban 1.2.3.4
this will generate an XML file in /tmp, that you can upload via the API that will
- first delete the firewall rule to drop traffic coming from object hostname-1.2.3.4 
- second delete the object def with name "hostname-1.2.3.4"

Notes

- hostname is always the name of the host running the xg_fail2ban command,
so in XG you can easily see what host a certain block originated from

- the rule is inserted in XG, after an existing rule, named "block from IPs".
You will need to create it. 

- the last line in xg_fail2ban can be uncommented, to actually upload the XML into XG

- the rules are created (default) in DISABLED state, to make sure nothing unexpected will happen.
For production you will need to have enabled rules :-)
