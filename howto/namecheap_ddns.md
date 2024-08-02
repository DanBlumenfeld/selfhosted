# Setting up DDNS on Namecheap

This is a quick-n-dirty set of instructions, mostly to remind myself how I set it up in my environment. Yours may, and probably will, differ.

### Assumptions:
 - You've purchased a domain (let's call it 'selfhosted123.com' for the sake of argument) from [Namecheap](https://www.namecheap.com).
 - You're using Namecheap BasicDNS, PremiumDNS, or FreeDNS: if you're using Namecheap to host a website, you're probably using Namecheap Web Hosting DNS, which doesn't support DDNS.

## Process

### Enable DDNS for your domain
1. Log into Namecheap, go to your Domain List, and select `Manage`.
1. Go to `Advanced DNS`.
1. Scroll down to the `Dynamic DNS` section.
1. Enable Dynamic DNS via the toggle to the right.
1. Make note of the Dynamic DNS Password that appears. For this example, let's use 'werewrw8ewr5qfffDr5'.

### Add DDNS host records
1. Log into Namecheap, go to your Domain List, and select `Manage`.
1. Go to `Advanced DNS`.
1. Scroll down to `Host Records`, select `Add New Record`.
1. Choose `Type` of `A + Dynamic DNS Record`.
1. For `Host`, enter @ if you want to apply the dynamic IP to your root domain, * if you'd like a wildcard for all non-existent subdomains, or put in a subdomain like 'vpn' or 'api' or whatever you like. For this example, let's use 'vpn'.
1. Put in a dummy IP address for `Value`, as it'll be updated by the DDNS client.
1. Leave `TTL` at Automatic unless you have a reason to do otherwise.

### Set up your client to update the DDNS record
#### Via HTTPS request
TL;DR: Using your preferred tools, send an HTTPS request that looks like this:
>https://dynamicdns.park-your-domain.com/update?host=[host]&domain=[domain_name]&password=[ddns_password]&ip=[your_ip]

Where `host`  equals the same thing you entered in the DDNS host record, `domain_name` equals your root domain, `ddns_password` equals the one supplied by Namecheap in their DDNS configuration, and `your_ip` equals the one you wish to assign to that host.

Using our example data, that request would look like:
https://dynamicdns.park-your-domain.com/update?host=vpn&selfhosted123.com&password=werewrw8ewr5qfffDr5&ip=127.0.0.1 (Please think carefully about using loopback as your public IP address, though...)

For more information, check out the [Namecheap docs](https://www.namecheap.com/support/knowledgebase/article.aspx/29/11/how-to-dynamically-update-the-hosts-ip-with-an-https-request/).

#### From your gateway
*I use a UniFi controller for my network, so the details will vary based on your infrastructure.*
1. In the Controller, go to the `Internet` section of `Settings`, then select your WAN.
1. Scroll down to the `Dynamic DNS` section, select `Create New Dynamic DNS`.
1. Enter the correct values:
    1. `Service`: select "namecheap".
    1. `Hostname`: enter the same thing as you entered in the DDNS host record, e.g. '@' or '*' or your subdomain. In this case, 'vpn'.
    1. `Username`: enter your root domain. In this case, 'selfhosted123.com'.
    1. `Password`: enter the value from Namecheap. In this case, 'werewrw8ewr5qfffDr5'.
    1. `Server`: enter 'dynamicdns.park-your-domain.com' to use Namecheap's DDNS server.
1. Save, and you're done.


#### Other ways I've not tried
 - [Namecheap Dynamic DDNS Client Software](https://www.namecheap.com/support/knowledgebase/article.aspx/111/11/using-namecheap-dynamic-dns-client-version-20x-beta/)
 - [ddclient](https://github.com/ddclient/ddclient)
 - [ddns-updater](https://github.com/qdm12/ddns-updater)
 - Check your router's manual

 ## Verify
 Check the public IP listed for your WAN in your gateway, or use a tool like [http://checkip.dyndns.org/](http://checkip.dyndns.org/), and then try resolving your DDNS-enabled hostname to verify that they match.