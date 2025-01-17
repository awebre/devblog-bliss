---
title: 'Cloudflare SSL Cert with Azure Container Apps'
date: 2023-03-24
summary: 'How to bind a custom domain name with Azure Container Apps with Cloudflare'
tags:
  - post
  - azure-container-apps
  - devops
  - hosting
  - azure
---

Azure Container Apps require an SNI/SSL Certificate to be bound in order for custom domain names to work. You can use Azure DNS Zones and [this helpful open source project](https://github.com/sjkp/letsencrypt-azure) to create automatically renewing certs via Let's Encrypt, but Azure DNS Zones cost money (as do the Azure Functions and other resources required for the automatically renewing cert). Instead, I opted to use Cloudflare's free Origin Certificates, which can be set to expire every 15 years and you can configure notifications for expiring certs.

### Azure Container Apps + Cloudflare

On Azure, go to your container apps environment and select Certificates -> "Add Certificate". Then go to Cloudflare, select the domain you would like to generate a certificate for and click SSL/TLS -> Origin Server. Click "Create Certificate", and then click "Create" (the defaults should be sufficient). From there you will be brought to a screen with the Origin Certificate and the Private Key encoded as text. Create a file on your desktop called your-domain.pem and paste the full text of the private key followed by the full text of the public key. **DO NOT LEAVE THIS PAGE UNTIL YOU HAVE SUCCESSFULLY UPLOADED AND BOUND YOUR CERT.**

With this certificate saved to disk, go back to the Azure Portal and upload your certificate. Then go to the Azure Container App that you wish to bind the custom domain to and go to Custom Domains. Add a custom domain, enter the information required to validate your domain (some of which will be DNS entries you need to enter in Cloudflare). Once you've validated your domain, you can bind the certificate by selecting the certificate you uploaded earlier to the ACA Environment.

In a matter of minutes, you should be serving your container app over your custom domain!
