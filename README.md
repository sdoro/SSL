
### 0. init repo and openssl

	# openssl version: 1.0.2g-1ubuntu4.6
	git init
	git add README.md
	git add openssl.cnf

### 1. setup the CA

	# edit openssl.cnf
	mkdir ./demoCA                    # main CA Directory
	mkdir ./demoCA/certs              # CA certificate
	mkdir ./demoCA/crl                # CA private key
	mkdir ./demoCA/newcerts           # 
	mkdir ./demoCA/private            # CA private keyS
	touch ./demoCA/index.txt          # CA serial number file
	echo 02 > demoCA/serial           #
	openssl genrsa -out cakey.pem -F4 1024
	openssl req -new -x509 -key cakey.pem -out cacert.pem -days 3650 \
		-config ./openssl.cnf
	mv cacert.pem ./demoCA            # CA certificate
	mv cakey.pem ./demoCA/private     # CA private key
	touch ./demoCA/certs/.gitkeep \
		./demoCA/crl/.gitkeep \
		./demoCA/newcerts/.gitkeep \
		./demoCA/private/.gitkeep
	git add demoCA

### 2. requesting/Creating/verifying a Certificate

	openssl genrsa -out userA/privkeyA.pem -F4 1024 -config ./openssl.cnf
	openssl req -new -key userA/privkeyA.pem -out userA/certA.csr \
		-config ./openssl.cnf
	openssl ca -in userA/certA.csr -out userA/cert-A.pem -config ./openssl.cnf
	openssl verify -CAfile demoCA/cacert.pem userA/cert-A.pem

### 3. encrypting with RSA

	mkdir userB
	openssl genrsa -out userB/privkeyB.pem -F4 1024 -config ./openssl.cnf
	openssl req -new -key userB/privkeyB.pem -out userB/certB.csr \
		-config ./openssl.cnf
	openssl ca -in userB/certB.csr -out userB/cert-B.pem
	openssl verify -CAfile demoCA/cacert.pem userB/cert-B.pem
	#
	echo "A private message" > message.txt
	openssl rsautl -encrypt -certin -inkey userB/cert-B.pem -in message.txt \
		-out ciphertext.ssl
	ls -l ciphertext.ssl
	openssl rsautl -decrypt -inkey userB/privkeyB.pem -in ciphertext.ssl \
		-out decrypted.txt
	cat decrypted.txt

