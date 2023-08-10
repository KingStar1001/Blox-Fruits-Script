# SSL/TLS Certificates with NGINX (Ubuntu 20.04) - [#blog](https://www.nginx.com/blog/using-free-ssltls-certificates-from-lets-encrypt-with-nginx/#Prerequisites)

### Prerequisites
- Install [NGINX](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-open-source/)
- Own or control the registered domain name for the certificate.
- Create a DNS record that associates your domain name and your server’s public IP address.

### 1. Download & Install Certbot
```
apt-get update
sudo apt-get install certbot
apt-get install python3-certbot-nginx
```

### 2. Set Up NGINX
- Assuming you’re starting with a fresh NGINX install, use a text editor to create a file in the `/etc/nginx/conf.d` directory named `domain‑name.conf` (so in our example, `www.example.com.conf`).
- Specify your domain name (and variants, if any) with the server_name directive:
  ```
  server {
      listen 80 default_server;
      listen [::]:80 default_server;
      root /var/www/html;
      server_name example.com www.example.com;
  }
  ```
- Save the file, then run this command to verify the syntax of your configuration and restart NGINX:
  ```
  nginx -t && nginx -s reload
  ```

### 3. Obtain the SSL/TLS Certificate
- Run the following command to generate certificates with the NGINX plug‑in:
  ```
  sudo certbot --nginx -d example.com -d www.example.com
  ```
- Respond to prompts from certbot to configure your HTTPS settings, which involves entering your email address and agreeing to the Let’s Encrypt terms of service.

  When certificate generation completes, NGINX reloads with the new settings. certbot generates a message indicating that certificate generation was successful and specifying the location of the certificate on your server.

  > Congratulations! You have successfully enabled https://example.com and https://www.example.com 
  > 
  > -------------------------------------------------------------------------------------
  > IMPORTANT NOTES: 
  > 
  > Congratulations! Your certificate and chain have been saved at: 
  > /etc/letsencrypt/live/example.com/fullchain.pem 
  > Your key file has been saved at: 
  > /etc/letsencrypt/live/example.com//privkey.pem
  > Your cert will expire on 2017-12-12.

  If you look at domain‑name.conf, you see that certbot has modified it:
  ```
  server {
    listen 80 default_server;
    listen [::]:80 default_server;
    root /var/www/html;
    server_name  example.com www.example.com;

    listen 443 ssl; # managed by Certbot

    # RSA certificate
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem; # managed by Certbot

    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot

    # Redirect non-https traffic to https
    if ($scheme != "https") {
        return 301 https://$host$request_uri;
    } # managed by Certbot
  }
  ```

_Example `domain‑name.conf` provided in this Repo. Compare and check with your `conf` file._
