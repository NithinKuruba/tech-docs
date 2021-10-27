# Generation of Self Signed Certificates

## Summary

1. Install openssl lib from www.openssl.org
2. Generate Key-Pair
3. Extract Public Key
4. Generate CSR (certificate signing request) file
5. Generate self-signed certificate

## Steps

### Generate Key-Pair

* Generate a keypair using RSA algorithm and 2048 bit length

  ```shell
  openssl genrsa -out sample.key 2048
  ```

### Extract Public Key

* Pass keypair as input and mention the file name to save the public key

  ```shell
  openssl rsa -in sample.key -pubout -out sample-public.key
  ```

### Generate CSR

* Usually in prod environments, a CSR file is generated and it is handed over to a CA (certification authority) and it is returned back signed, which makes it CA signed officially. 

* In non prod environments, we shall sign it by ourselves

* Generate a CSR file

  ```shell
  openssl req -new -key sample.key -out sample.csr
  ```

* Verify CSR file

  ```shell
  openssl req -text -in sample.csr -noout -verify
  ```

### Generate Self-Sign Cert

* Use x509 to sign the cert

  ```shell
  openssl x509 -in sample.csr -out sample.crt -req -signkey sample.key -days 365
  ```

  

