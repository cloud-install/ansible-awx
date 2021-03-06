# SSL/HTTPS

AWX deployment package has installed the SSL module of Nginx  

## Preparation

1. Enabled the TCP:443 port on your Cloud Platform
2. Completed the Domain Name resolution
3. Have applied for a CA certificate

## Configure HTTPS

1. Modify or check your ***/data/.awx/docker-compose.yml*** file, make sure add items "- 443:443" and  "- /data/cert:/etc/ssl/certs"
    ```
    ports:
    - "80:8052"
    - "443:443"  # for HTTPS port
    hostname: awxweb
    user: root
    restart: unless-stopped
    volumes:
    - /data/cert:/etc/ssl/certs  # for store your CAs
    ```
2. Upload your certificate to the directory: */data/cert* of your Server

3. Insert the **HTTPS template** into `server{}` of file: ***/data/.awx/nginx.conf***
   ``` text
   #-----HTTPS template start------------
   listen 443 ssl; 
   ssl_certificate /etc/ssl/certs/xxx.crt;
   ssl_certificate_key /etc/ssl/certs/xxx.key;
   ssl_trusted_certificate /etc/ssl/certs/chain.pem;
   ssl_session_timeout 5m;
   ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
   ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
   ssl_prefer_server_ciphers on;
   #-----HTTPS template end------------
   ```
   > Make sure the ssl_certificate directory begin with **/etc/ssl/certs**, not **/data/cert**

4. Restart dockers by the following commands
   ```
   cd /data/.awx
   sudo docker-compose up -d
   docker restart awx_web
   ```

## FAQ

#### Where are the certificates stored?

The certificate is stored in persistent storage, but the path in the configuration file must be the path of the container virtual machine