Self Signed SSL Certificate Generator
=========
**DO NOT USE THIS** if you handle sensitive information such as personally-indentifyable information or payment card data. The generated keys are only suitable to protect against passive snooping. Connections can still be MITM'd by an active adversary because these certificates are not signed by a trusted CA.

This will build a self-signed SSL keypair and write them out to, by default, `/etc/ssl/ssl_domain/{key,csr,cert}.pem`.  These keys are intended primarily to be put on a front-end behind CloudFlare in "Full" SSL mode; see [option 1](https://blog.cloudflare.com/origin-server-connection-security-with-universal-ssl/) in CloudFlare's SSL blog.


Requirements
------------

OpenSSL must be installed on the system. This does not attempt to install it.

Role Variables
--------------

	self_signed_ssl_domain: ""
	
	self_signed_ssl_country: ""
	self_signed_ssl_state: ""
	self_signed_ssl_city: ""
	self_signed_ssl_org: ""
	
	self_signed_ssl_dir: /etc/ssl/{{ self_signed_ssl_domain }}
	self_signed_ssl_dh_size: 2048
	self_signed_ssl_key_size: 2048
	self_signed_ssl_ecc: no
	
	# You probably will not need to change these
	self_signed_ssl_key_file: key.pem
	self_signed_ssl_csr_file: csr.pem
	self_signed_ssl_cert_file: cert.pem
	self_signed_ssl_dhparam_file: dhparam.pem
	
	# Public key lifetime in days; defaults to 5 years
	self_signed_ssl_cert_lifetime: 1825

To skip Diffie-Hellman params file generation, set `dh_size` to 0.

To generate an EC private key, set `ecc` to `yes`.

Example Playbook
----------------
    - hosts: servers
      roles:
      - role: Firehed.self_signed_ssl
        self_signed_ssl_domain: example.com
        self_signed_ssl_country: US
        self_signed_ssl_state: California
        self_signed_ssl_city: San Francisco
        self_signed_ssl_org: Example Org
License
-------

MIT
