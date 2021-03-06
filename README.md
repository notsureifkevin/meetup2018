# meetup2018
E2E Encryption using Traefik, and Boulder

Generate root and intermediate certs:

```bash
./bootstrap.sh
```
* Note: Passphrase is set to `meetup2018` for keys

Based on configs and process from:

https://jamielinux.com/docs/openssl-certificate-authority/index.html

This creates the required boulder certs, and bakes them into the docker image:

Boulder image: `dtomcej/boulder:meetup2018`

https://github.com/dtomcej/boulder

This boulder image has the latest files baked in, and has a custom servers configuration to allow it to run in kubernetes.

Although, this image is ~1.35G, whereas the base image is 550M. You probably want to just clone my repo and `docker build .` yourself ;)

Boulder folder has 3 parts:

* Mysql/Maria
* HSM - Keystore
* Boulder itself

We use an init container to wait for requirements to be read before starting pod. This coupled with good readiness probes allows evaluation order independance.

(Ensure traefik.rocks and traefik2.rocks resolves to localhost either by your `/etc/hosts` file, or DNS A records in your resolver).

We will also require the intermediate cert in `certs/browser` to be added and trusted by your client/browser.

Add:
`/certs/browser/intermediate.crt`

to trust store (SSL and x.509 Basic)

Browse to: `https://traefik.rocks:30443/`

and

`http://localhost:30880/dashboard/`

Scaling app:

`kubectl scale deployment whoami -n whoami --replicas=10`