These scripts will allow for XG in WAF, where the internal WAF-ed host ("webserver") can block IPs using fail2ban,
and configure the actual block to be on the XG, using the API.

Fail2ban and such will no longer work with WAF, as all traffic comes from XG, and not from the original host.
Be sure to pass the the X-Forwarded-For to the internal server.

Instructions, on the to-be protected internal server:
* create a directory /opt/xg_fail2ban
* copy all files to it, except:
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

Notes

- hostname is taken from /etc/hostname, and is only so you can easily see in XG where the ban comes from

- the rule is inserted in XG, after an existing rule, named "block from IPs".
You will need to create it, otherwise the script has no idea where and what group to put the rule in

- the last line in xg_fail2ban can be commented/uncommented, to actually upload the XML into XG or not.

- the rules are created (default) in DISABLED state, to make sure nothing unexpected will happen.
For production you will need to have enabled rules. Configure this in the conf file.

- I need to find out and document how to connect xf_fail2ban to fail2ban, so that fail2ban will use it for banning/unbanning

Troubleshoot:
- make sure to enable XG for API access from the internal server IP
- manually try to upload the XML to XG, to see return codes
- check that the last line is uncommented if nothing happens
