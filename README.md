# Kubernetes

Kubernetes Security
-------------------
TLS :- Transport layer security.

Symmetric encryption :- type of encryption that uses the same key to encrypt and decrypt the data. Risk of hacker to get access.

Asymmetric encryption :- type of encryption that uses concept of private key and public key (public lock).

if we encyrpt data using public lock then we can decrypt it using private key only.

eg :- access of server

step-1 : on client machine we'll generate key ----> ssh-keygen, it will generate id_rsa(private key) and id_rsa.pub(public key).

step-2 : on server, we have to add that public key (public lock) so that we can get access of it using private key ----> for that we'll place the public lock file in ~/.ssh/authorized_keys

step-3 : ssh to the server by specifying private key location :- ssh -i id_rsa user1@server1

###########################################################################
how to securely send private key using symmetric encryption :-

with asymmetric encrption, we have successfully sent sender's (browser) private key to receiver and using symmetric encryption we have secured all the future communication.

whenever a user do ssh, it gets server public key ----> then using that public key (public lock) user can send his private key to server ----> then using server private key , server can unlock it and can gets user private key.

cmd :- openssl genrsa -out my-bank.key 1024 --- (generates private key)

cmd :- openssl rsa -in my-bank.key -pubout > mybank.pem ----- (generates public key)

 ##########################################################################
 
 Hacker can find new way to hack messages :-

can create actual replica of original website ------> own private and public key and can hack messages .

verifiaction of certicate :-

self signed certificate :- signed by yourself.

All of the web browsers are built in with a certifiaction validation mechanism to find it whether it is legitmate or not.

Certificates Autority is responsible for signing the certificate and browser can trust on that.

By using the key and domain name we can generate a CSR file then this file will be sent to CA authority for validation then they sign the certificate and send it back to you.

How browser knows the CA itself is legitmate :-

CA use their private keys to sign the certificatesn and the public keys of all the CA's are built in to the browser.

1. Admin generating key-pair for securing SSH.
2. Web-server generates key-pair for securing website with HTTPS.
3. CA generates its own set of key-pair to sign certificate.
4. End-user though only generates a single symmetric key.

The whole infrastructure, including the CA, the servers, the people, and the process of generating, distributing and maintaining digital certificates is known as public key infrastructure (PKI).

we can encrypt data using any private or public key and decrypt with other.

certificates with public key uses extension of *.crt and *.pem

certificates with private key uses extension of *.key and *-key.pem

#################
TLS in kubernetes
#################

1. Root Certificates :- used by CA authorites to sign server certificates.
2. client certificates :- by using this certificates server verifies the client.
3. server certificates :- configured on the server.

services to use server certificates.

client to use client certificates.

#########################
TLS certificate creation
#########################
 
steps for generating CA certificates:-

1. using open ssl generate key :- openssl genrsa -out ca.key 2048
2. generate signing request (certificate with no sign.) :- openssl req -new -key ca.key -subj "/CN=KUBERNETES-CA" -out ca.csr
3. sign the certificate :- openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt 

steps for generating admin user certificates:-

In this case 3rd step will be different, we sign the certificate using ca.key and ca.crt.

#########################################
view certificates in a kubernetes cluster
#########################################

To view the details of the certificate

$ openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout

###################
Inspect server logs
###################

1. Inspect server logs using journalctl

$ journalctl -u etcd.service -l

2. View logs using kubectl

$ kubectl logs etcd-master

3. View logs using docker ps and docker logs

$ docker ps -a
$ docker logs <container-id>

#######################
  Certificates API
#######################
  
############
 Admin user
############
 
whenever a new user want to access k8s cluster, they generates a key and csr and send csr to admin, then admin sends this csr to CA server for signing it. then signed CSR will be returned to user.     

CA Server :- CA is just a pair of key and certificate files we have generated whoever gains access to these pairs of files can sign any certificate for the k8s env. , these files needs to be place on a server that is fully secure and we call that server as CA server. As of now certificates are placed on master node itself, so that is our CA server.
 
Kubernetes has its built-in certificates API, with this API you can send a certiifcate signing request directly to k8s through an API call. then CertificateSigningRequest object will be created instead of logging into server, then these requests can be reviewed and approved easily by kubectl cmds.
 
 Steps :-

1. openssl genrsa -out jane.key 2048

2. openssl req -new -key jane.key -subj "/CN=jane" -out jane.csr
 
3. Admin takes above key and creates certificate signing request object.
  














































































































