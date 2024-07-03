## Why not use "autocert" built into dumbproxy?
To run https proxy on a different port than 443 and autocert doesn't support getting certificates for non-standard ports. For this we have to run a separate process to get the certificate and then use it in the proxy server.

## Using DNS Challenge to get certificates
Since we are running the proxy server on a non-standard port, we can't use HTTP challenge to get the certificate. We have to use DNS challenge to get the certificate. This requires setting up a DNS server and a DNS provider that supports API to update DNS records. 

In this particular case we are using certbot with cloudflare DNS plugin to get the certificate. 

## How to use this
1. Update `./certbot/cloudflare.ini` file with your cloudflare API key.
2. Add your domain name to `./env` file.
3. Get the initial certificate.
  ```bash
  docker run --rm -it -v ./certbot/cloudflare.ini:/cloudflare.ini:ro -v ./certbot/letsencrypt:/etc/letsencrypt:rw certbot/dns-cloudflare:v2.11.0 certonly -d <PROXY_DOMAIN_NAME> --dns-cloudflare --dns-cloudflare-credentials=/cloudflare.ini --register-unsafely-without-email --agree-tos
  ```
4. Create credentials file to be used by dumbproxy.
  ```bash
  htpasswd -c dumbproxy.htpasswd <USERNAME>
  ```
5. Start the compose stack.
  ```bash
  docker-compose up -d
  ```
