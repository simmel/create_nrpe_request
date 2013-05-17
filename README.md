# create\_nrpe\_request

A script to create raw binary NRPE requests to send over the wire for e.g. F5 BIG-IP healthcheck usage.
This script is a Perl "port" of http://blog.cactiusers.org/2012/02/18/nagios-nrpe-client-via-php/

## USAGE

```sh
$ ./make_nrpe_request --nrpe-check check_amqp
Request in hex: \x00\x02\x00\x01\xEF[...]\x00\x53\x52
Reply to match for: Received message 'test_message'
REMEMBER that there are random and NULL characters before and after the text above in the reply.
$
```

### For F5 BIG-IP

1. Run the script and make note of the `Request in hex` output
1. Create a new health monitor, `Local Traffic => Monitors => +`
  * Use `Type => HTTPS`
  * Select `Configuration => Advanced`
  * Set `Send String` to the string that you got from the script
  * Set `Receive String` to what you got from the script or what you prefer it to match against
  * Set `Receive Disable String` if you so desire, if you don't set it everything not matching `Receive String` will create a failure
  * Set `Cipher List` to `-ALL:ADH:-EXP:-LOW:-3DES:-SEED@STRENGTH` (which should be AES and RC4, in that order. Check with OpenSSLs [ciphers(1)](https://www.openssl.org/docs/apps/ciphers.html))
  * Set `Alias Service Port` to `5666`
  * Press `Finished`.
1. Go to your pool `Local Traffic => Virtual Servers => Pools`
  * Under `Health Monitors` put your newly created health monitor to `Active`
  * Press `Update`
1. Press `Update` until `Availability` is `Available (Enabled) - The pool is available`
