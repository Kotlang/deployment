# Kotlang Services Deployment
This repository contains the deployment files for the Kotlang services.

## File Details
- `docker-compose.yml` - The docker compose file for the services. This file is used to deploy the services on a single machine. 

In docker-compose an independent network is created. One service can call another service by using the service name as the hostname. For example, the `socialGo` service can call the `authGo` service by using the hostname `authGo`. These calls happen over the internal network created by docker-compose and doesn't require any port mapping. This reduces the complexity of the deployment and number of hops required to make a call.

- `nginx.conf` - The nginx configuration file. This file is used to configure the nginx server. The nginx server is used to route the requests to the appropriate service. It uses sub-domain based routing. For example, the `socialGo` service is available at `social.neptune.co`. 

Only ports of the nginx server are exposed to the outside world.

## LetsEncrypt and Certbot
LetsEncrypt provides free SSL certificates. Certbot is a tool to automate the process of getting and renewing the SSL certificates. Certbot is used to get the SSL certificates for the sub-domains. The certificates are stored in the `certs` directory. The certificates are renewed automatically by certbot.

nginx and certbot interact with each other through shared volumes.

Certbot command to get certificate:
```
root@kotlang-ubuntu:~/kotlang-cluster# docker compose run --rm  certbot certonly --webroot --webroot-path /var/www/certbot/ -d navachar.co
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Requesting a certificate for navachar.co

Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/navachar.co/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/navachar.co/privkey.pem
This certificate expires on 2023-03-16.
These files will be updated when the certificate renews.

NEXT STEPS:
- The certificate will need to be renewed before it expires. Certbot can automatically renew the certificate in the background, but you may need to take steps to enable that functionality. See https://certbot.org/renewal-setup for instructions.

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
If you like Certbot, please consider supporting our work by:
 * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
 * Donating to EFF:                    https://eff.org/donate-le
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
```

`docker compose run` enables us to run individual commands in a service. The `--rm` flag removes the container after the command is executed.