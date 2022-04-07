# SSL Certificates

- (Most common OpenSSL commands)[https://www.sslshopper.com/article-most-common-openssl-commands.html]

Generate RSA key

    openssl genrsa -out domain.com.key 4096

Create a CSR

    openssl req -new -sha256 -key domain.com.key -out domain.com.csr

Check private key

    openssl rsa -in server.key -check

Check a public key

    openssl rsa -inform PEM -pubin -text -noout -in pub.key
    openssl pkey -inform PEM -pubin -text -noout -in pub.key

Verify CSR

    openssl req -text -noout -verify -in server.csr

Generate self-signed cert

    openssl x509 -req -sha256 -days 365 -in server.csr -signkey server.key -out server.crt

Check a certificate

    openssl x509 -text -noout -in server.crt

Verify a certificate and key matches

    openssl x509 -noout -modulus -in server.crt | openssl md5
    openssl x509 -inform der -modulus -noout -in cert.crt | openssl md5
    openssl rsa -noout -modulus -in server.key | openssl md5

## View PEM encoded certificate

    openssl x509 -text -noout -in cert.pem

If you get the folowing error it means that you are trying to view a DER encoded certifciate and need to use the commands in the “View DER encoded certificate below”

    unable to load certificate
    12626:error:0906D06C:PEM routines:PEM_read_bio:no start line:pem_lib.c:647:Expecting: TRUSTED CERTIFICATE

View DER encoded Certificate

    openssl x509 -inform der -text -noout -in certificate.der

If you get the following error it means that you are trying to view a PEM encoded certificate with a command meant for DER encoded certs. Use a command in the “View PEM encoded certificate above

    unable to load certificate
    13978:error:0D0680A8:asn1 encoding routines:ASN1_CHECK_TLEN:wrong tag:tasn_dec.c:1306:
    13978:error:0D07803A:asn1 encoding routines:ASN1_ITEM_EX_D2I:nested asn1 error:tasn_dec.c:380:Type=X509

Check a PKCS#12 file (.pfx or .p12)

    openssl pkcs12 -info -in keyStore.p12

## View DER encoded certificate

base64 -D sevi-dev_saml_public_cert.der | openssl x509 -text -inform DER

## Transform

Transforms can take one type of encoded certificate to another. (ie. PEM To DER conversion)

PEM to DER

    openssl x509 -in cert.crt -outform der -out cert.der

DER to PEM

    openssl x509 -in cert.crt -inform der -outform pem -out cert.pem

## Combination

Generate public key from private key

    openssl rsa -in private.pem -out public.pem -pubout

## Generate PKCS#1 key

To generate a PKCS#1 key the openssl genrsa command can be used.
Using openssl req to generate both the private key and the crt will end up with a PKCS#8 key.

Converting from PKCS#8 to PKCS#1 can be done with

    openssl rsa -in key.pem -out key.pem

Converting the other way can be done with

    openssl pkey -in key.pem -out key.pem

## Get certificate from endpoint

```shell
openssl s_client -connect server-to-connect-with-cert:443 < /dev/null 2> /dev/null | openssl x509 -outform PEM > cert.crt
```

## Test connection with certificate

```shell
openssl s_client -connect server-to-connect-with-cert:443 -cert certificate-to-be-used.pem -key certificate-to-be-used.key -state -quiet
```
