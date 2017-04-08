
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

