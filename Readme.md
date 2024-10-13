# RBAC

## Extract the CA key and certificate of Kubernetes Kind cluster

   docker cp controlplane-pod:/etc/kubernetes/pki/ca.crt .  <br />
   docker cp controlplane-pod:/etc/kubernetes/pki/ca.key .
   
## Steps to add Kubernetes user named as kubeusr

## (1) Generate User private key
        openssl genrsa -out kubeusr.key 2048

## (2) Lets now create a Certification Signing Request (CSR) for kubeusr. When you generate the csr make sure you provide following information

        CN: This will be set as username
        O: Org name. This is actually used as a group by kubernetes while authenticating/authorizing users. You could add as many as you need

        openssl req -new -key kubeusr.key -out kubeusr.csr -subj "/CN=kubeusr/O=dev/O=kubeusr.org"

## (3) Sign the CSR (Cert signing request) with Certificate Authority key(ca.key) and crt (ca.crt) located in minikube home dir ~.minikube/)
        openssl x509 -req -CA ca.crt -CAkey ca.key -CAcreateserial -days 730 -in kubeusr.csr -out kubeusr.crt

## (4) Add user credentials in Kubeconfig - Add entry for user in Kubeconfig
        kubectl config set-credentials kubeusr --client-certificate=kubeusr.crt --client-key=kubeusr.key

## (5) Set Context For Kubernetes cluster: 
        <syntax>: nameofcontext,name of kubernetes cluster to which context to be set and user to be added
        kubectl config set-context kubeusr-kubernetes --cluster=minikube --user=kubeusr


