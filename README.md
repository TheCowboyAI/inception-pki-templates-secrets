# inception-pki-templates-secrets
Work in progress

## Justification for this repo:
When bootstraping a new [CIM]() organisation we need to define secrets and initial configurations in a controlled manner and store and treat them safely.

## Terminology:
 - [Key-Node]() - Offline laptop booted from an luks-encrypted flash-card/usb-stick written with an ad-hoc generated image using the CowboyAI repository.
   - Used in initial Inception
   - Used together with the Root CA yubikeys to handle subsequent key operations (revocation, new keys, replacements, etc.).
 - [tls-pgp-dns-Node]() - Online vm/computer responsible for handling organisation's:
   - [TLS certificate operations](https://smallstep.com/docs/step-ca/provisioners/#remote-provisioner-management) - issue/renue/revoke ACME and x509 certificates for servers and services.
   - [OpenPGP public keyserver](https://github.com/hockeypuck/hockeypuck) - Distribute OpenPGP public keys in a trusted manner.
   - [DNS resolution](https://technitium.com/dns/) - Needed for both DNS resolution and for issuing of ACME and x509 certificates (DNS-01 challenge).

## What is this repository for:
 - This repo is cloned/injected in the KeyNode image and used in the [C.I.M.]() PKI inception process.
 - This repo also acts as the base for the user's private future secrets repo that will need to be created.
 - Files generated during the process will have to be transfered some into Yubikeys and some into git repositories.

### **On the [Key-Node]() isolated environment laptop the following secrets and related configurations will be generated:**

**Secrets**, passwords and public/private certificates:
 - **TLS Root CA** - key and password - **stored offline on a Yubikey.**
   - Will be generated using either:
     - [OpenSSL](https://jamielinux.com/docs/openssl-certificate-authority/introduction.html) for fear of breach on the other option (step-ca) **or**
     - [Smallstep's step-ca](https://smallstep.com/docs/step-ca/) which is the solution that **will operate** with the subsequent certificates.
     - Possibly signed by PGP key ?
 - **TLS Intermediate Root CA** key and password
    - Will be generated using either option from above.
    - Stored online
      - In a private git repository with it's contents encrypted with [AGE](https://github.com/FiloSottile/age) and protected by a password/below SSH private_key.
      - The secret_key and the password for it are required on the tls-pgp-dns-Node for normal operation of the services.
    - Used online by step-ca to programatically issue ACME, x509 and SSH certificates (using the above secret_key and password).
    - Possibly signed by PGP key ?
 - **SSH KeyPair** - Will be used to access the private git repository and to decrypt the git repository's encrypted files on a new NODE (eg: tls-pgp-dns-Node).
   - Issued at this step so that it is certainly new and has a traceable source.

 - **GPG Root CA** - key and password - **stored offline on a Yubikey**.
 - **GPG Intermediate Root CA**
    - Stored on Key-Node image (the bootable flash-card/usb-stick).
    - Used to generate subsequent certificates/keys for employes.
    - Public certificates/keys will have to be published online using [hockeypuck](https://github.com/hockeypuck/hockeypuck) OpenPGP public keyserver.




**Configuration files**:
 - step-ca TLS configuration with the **TLS Intermediate Root CA** installed, stored online as described above
   - Needed for the services to start.
   - Has to be transfered from KeyNode using a USB stick (files are already encrypted) to a computer with internet access.
     - Has to be pushed into the organisation's secrets git repository.
 -






## How to use this repo
Used by the inception-nodes to bootstrap the pki
