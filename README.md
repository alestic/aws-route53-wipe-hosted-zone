# aws-route53-wipe-hosted-zone

Delete an Amazon Route 53 Hosted Zone with all contained Record Sets

To wipe out the Route 53 entries for "example.com", you might use:

    aws-route53-wipe-hosted-zone example.com

WARNING! This is a simple example script with no error checking and no
prompting before doing dangerous tasks. Everything this tool tries to
do is dangerous if you give it a domain name that you don't want to
wipe out.

