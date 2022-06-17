# Working with .pfx files

## Generate .pfx from .crt and .key

```bash
openssl pkcs12 -export -out yourfile.pfx -inkey yourfile.key -in yourfile.crt
```

## Extract Private Key from .pfx

```bash
openssl pkcs12 -in [yourfile.pfx] -nocerts -out [drlive.key] -nodes
```

## Extract Certificate from .pfx

```bash
openssl pkcs12 -in [yourfile.pfx] -clcerts -nokeys -out [drlive.crt] -nodes
```