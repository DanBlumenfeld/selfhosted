# Home Assistant Networking Notes

## Failure to resolve DNS defined by your local router
This annoyed the crap out of me for a couple of hours, until I settled down and actually fired up a terminal in my Home Assisatant OS instance.

### The situation
I had defined a local DNS redirect in my UniFi controller at 192.168.1.1, pointing 'ha.mydomain.com' to local address 192.168.1.2. I did this so that I'd be able to issue a Let's Encrypt certificate against a publicly verifiable domain, but use it in my local network to secure communications.

I had successfully set up the certificate via the [Let's Encrypt add-on](https://github.com/home-assistant/addons/blob/master/letsencrypt/DOCS.md) in Home Assistant, and configured Home Assistant to use the correct hostname and require HTTPS.

I was now able to browse to 'https://ha.mydomain.com:8123' and access my Home Assistant instance, so, yay!

However...I wanted to set up a [Certificate Expiry](https://www.home-assistant.io/integrations/cert_expiry/) sensor so that I could automatically renew as needed. When trying to do so, I received an error indicating that the hostname could not be resolved...pretty funny, as I was receiving said error in a browser that was successfully resolving said hostname.

Turned out that, if I shelled into the HAOS instance and tried to resolve 'ha.mydomain.com', it failed, because HAOS was bypassing the DNS server assigned via DHCP and going right to its own preferred server.

Running `ha dns info`, I noted that no servers had been defined. Well, there's your problem!

### The (easy) fix
I issued the command `ha dns option --servers dns://192.168.1.1`, then `ha dns restart`. At that point, an nslookup on 'ha.mydomain.com' returned the correct internal address of 192.168.1.2, and I was able to set up the Certificate Expiry sensor using the proper hostname.