# SSL Certs

This repository contains ssl certificates we use in multiple cases. We use them as a certificate for our [dev docker images-v2](https://github.com/labor-digital/docker-base-images-v2) as well as for things like the [webpack dev server in our asset-builder](https://github.com/labor-digital/asset-building).

**WARNING: This is a DEV utility! It should not be considered, save for production!**

## Why? 
Using a single root ca for all our dev needs it is possible to add the ca as "trusted authority", meaning no more pesky browsers whining about "insecure certificates".

## Usage
If you simply want to add the root ca to your store, you can use this guide.
You can find the file you want to add under /rootca

### Windows 10: Chrome, IE11 & Edge
Windows 10 recognizes .crt files, so you can right-click on RootCA.crt > Install to open the import dialog.

Make sure to select "Trusted Root Certification Authorities" and confirm.

You should now get a green lock in Chrome, IE11 and Edge.

### Windows 10: Firefox
There are two ways to get the CA trusted in Firefox.

The simplest is to make Firefox use the Windows trusted Root CAs by going to about:config, and setting security.enterprise_roots.enabled to true.

The other way is to import the certificate by going to about:preferences#privacy > Certificats > Import > RootCA.pem > Confirm for websites.

## npm
You can simply require the latest version of this repository through npm:

```npm install @labor-digital/ssl-certs```

## Where is the LaborRootCA.key?
We store that in our private vault, somewhere at the north-pole, so nobody can do funny stuff with it :) 
**Hint: LABOR_ADMIN_SHARE/SSL**

## How to make a new cert
Basically follow [this guide](https://gist.github.com/cecilemuller/9492b848eb8fe46d462abeb26656c4f8).

The codes looks like this:
```
openssl req -x509 -nodes -new -sha256 -days 18250 -newkey rsa:4096 -keyout LaborRootCA.key -out LaborRootCA.pem -subj "/C=DE/CN=LABOR-digital-RootCA"
openssl x509 -outform pem -in LaborRootCA.pem -out LaborRootCA.crt
```

### Localmachine.space
domains.ext:
```
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
subjectAltName = @alt_names
[alt_names]
DNS.1 = localmachine.space
DNS.2 = *.localmachine.space
```

Generate the cert:
```
openssl req -new -nodes -newkey rsa:4096 -keyout localmachine.space.key -out localmachine.space.csr -subj "/C=DE/ST=RLP/L=Mainz/O=LABOR-digital/CN=localmachine.space"
openssl x509 -req -sha256 -days 18250 -in localmachine.space.csr -CA LaborRootCA.pem -CAkey LaborRootCA.key -CAcreateserial -extfile domains.ext -out localmachine.space.crt
```

### localhost
domains.ext:
```
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
subjectAltName = @alt_names
[alt_names]
DNS.1 = localhost
DNS.2 = *.localhost
IP.1 = 127.0.0.1
```

Generate the cert:
```
openssl req -new -nodes -newkey rsa:4096 -keyout localhost.key -out localhost.csr -subj "/C=DE/ST=RLP/L=Mainz/O=LABOR-digital/CN=localhost"
openssl x509 -req -sha256 -days 18250 -in localhost.csr -CA LaborRootCA.pem -CAkey LaborRootCA.key -CAcreateserial -extfile domains.ext -out localhost.crt
```