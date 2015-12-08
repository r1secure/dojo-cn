# DOJO CONFIANCE NUM / liste des commandes

## openssl... 

> openssl version

> openssl toto

## chiffrement symétrique AES-256-CBC
> echo 'ceci est un message secret' > data.txt

> openssl aes-256-cbc -nosalt -p -in data.txt -out data.enc

> cat data.enc

## déchiffrement AES-256-CBC
> openssl aes-256-cbc -d -in data.enc -K XXXXX -iv XXXXX

## hachage
> openssl sha256 data.txt 

> openssl sha1 data.txt 

> openssl md5 data.txt 

## Génération du bi-clé
> openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -aes-256-cbc -out mykey.pem


## Affichage du bi-clé
> cat mykey.pem

> openssl rsa -in mykey.pem -text -noout

## Export la clé « pub »
> openssl rsa -in mykey.pem -pubout -out mykey.pub.pem

## Chiffrement du message data.txt
> openssl pkeyutl -encrypt -pubin -inkey mykey.pub.pem -in data.txt -out data.rsa

> cat data.rsa

## Déchiffrement du message data.txt
> openssl pkeyutl -decrypt -inkey mykey.pem -in data.rsa

## Performances
> openssl speed rsa aes-256-cbc

## une signature pas à pas
> openssl sha256 -binary data.txt > data.sha256

> cat data.sha256 | openssl pkeyutl -sign -inkey mykey.pem -out data.sha256.sig

> diff data.sha256 data.sha256.valid

## signature avec pkeyutl
> openssl pkeyutl -sign -in data.sha256 -inkey mykey.pem -out data.sha256.sig

> openssl pkeyutl -verify -pubin -inkey mykey.pub.pem -in data.sha256 -sigfile data.sha256.sig

## AC
> openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -out CA-key.pem

> openssl req -new -key CA-key.pem -config openssl.cnf -out CA-req.pem

> openssl x509 -req -in CA-req.pem -signkey CA-key.pem -extensions ca_ext -days 3652 -out CA-cert.pem

> openssl x509 -in CA-cert.pem -noout -text
	
> openssl pkcs12 -export -out CA-cert.p12 -inkey CA-key.pem -in CA-cert.pem

## certificats utilisateurs
> openssl rand -hex 8 -out CA-crt.srl

> openssl req -new -key mykey.pem -out mykey-smime.csr

> openssl x509 -req -in mykey-smime.csr -CA CA-cert.pem -CAkey CA-key.pem -CAserial CA-crt.srl -sha256 -days 730 -out mykey-cert-smime.pem

> openssl pkcs12 -export -in mykey-cert-smime.pem -inkey mykey.pem -certfile CA-cert.pem -name "ELL dojo cert" -out mykey-cert-smime.p12

> openssl x509 -fingerprint -in CA-cert.pem -noout

