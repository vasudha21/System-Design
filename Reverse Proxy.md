# Reverse Proxy System Design

## Table of Contents
1. [Introduction](#introduction)
2. [Reverse Proxy Features](#reverse-proxy-features)
3. [Design Usecase](#design-usecase)
   - [Security](##security)
   - [Load Balancing](##load-balancing)
   - [SSL Encryption and Cipher Suites](##ssl-encryption-and-cipher-suites)
   - [Caching](##caching)

## Introduction <a name="introduction"></a>
Reverse proxy sits between multiple servers. When a client sends a request to the origin server of the website, the request is then intercepted by the server proxy.
Eg. I type in the web browser https://www.leetcode.com/VasudhaMishra The browser makes a DNS query to find the IP address of leetcode.com. If leetcode.com uses reverse proxy and configures it correctly, the DNS query will return the reverse proxy IP address.
I will be using nginx for reverse proxy design.

![Ohh... So this is how reverse proxy works. Now I know. :)](https://linuxhandbook.com/content/images/2020/09/deploy-multiple-services-with-nginx-reverse-proxy-container.png)

## Reverse Proxy Features <a name="reverse-proxy-features"></a>
1. **Backend Anonymity:** Backend servers remain hidden from external networks and vulnerability.
2. **DDoS Mitigation:** Many proxies have built-in features to shield backend servers from distributed denial-of-service attacks. Eg IP denies listing and rate limiting.
3. **SSL Offloading:** Handle decryption of incoming request and encryption of server response.
4. **Data Compression:** Reduce the size of server response.
5. **Response Caching:** Introduce cache for previous response to improve speed and reduce load.
6. **Direct Serving of Static Content:** Manage the delivery of static file (eg HTML, CSS, Javascript, Image, Video) directly to the client.
7. **URL Rewriting:** Modify URL content before forwarding it to the backend server.

## Design Usecase

- **Security** : 
     Security in the context of a reverse proxy involves safeguarding web applications from cyber threats.

    A reverse proxy acts as a protective barrier, implementing security measures such as SSL/TLS encryption, HTTP security headers, and web application firewall capabilities. It helps ensure data integrity, confidentiality, and protection against various attacks like XSS and rate limiting.
    | Property                               | Description                                                                                                      |
    | -------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
    | SSL/TLS protocol and Cipher Suites      | Use Latest TLS version. Enable perfect forward secrecy (new temporary private key per session), disable outdated and insecure protocols like SSLv2 and SSLv3. |
    | SSL Certification Configuration         | Configure SSL certificate and private key.                                                                        |
    | HSTS (HTTP Strict Transport Security) Header | To force secure connection.                                                                                       |
    | SSL HSTS Preload                        | Submit domain to HSTS preload.                                                                                    |
    | OCSP (Online Certificate Status Protocol) | For faster and reliable certificate.                                                                              |
    | SSL Session Cache and Timeout           | Decide per requirement.                                                                                           |
    | SSL Buffer Size and Timeout             | Decide per requirement.                                                                                           |
    | SSL Early Data (Optional)               | Enable SSL early data for faster page load.                                                                       |
    | SSL Termination                         | Terminate SSL at the load balancer if using one, to offload encryption/decryption from the backend server.       |
    | Cipher Suite Configuration              | Fine-tune SSL cipher.                                                                                             |

- **Load Balancing** :
     Load balancing aims to distribute incoming network traffic across multiple servers to ensure optimal resource utilization and prevent server overload.

    A reverse proxy efficiently manages this distribution, enhancing the availability and reliability of web applications. It directs client requests to different backend servers, enabling efficient utilization of resources and ensuring high performance and scalability.

    | Property                               | Description                                                                                                      |
    | -------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
    | Load Balance Configuration              | Use the upstream block to define the backend server and load balancing algorithm.                                 |
    | HTTP Load Balancing                     | Configure the main “server” block for HTTP load balancing.                                                        |
    | Non-HTTP Protocol (TCP/UDP)             | Use the stream block for TCP or UDP load balancing.                                                               |
    | Health Check                            | Implement health checks to ensure backend servers are available.                                                  |
    | Session Persistence                     | Use “ip_hash” directive for session persistence based on the client IP.                                            |
    | SSL Termination                         | Terminate the SSL load balancer for improved performance.                                                         |
    | Load Balancer Metrics and Monitoring     | Enable stub status for monitoring.                                                                                |
    | Connection Handling and Buffering        | Optimize connection handling and buffer settings.                                                                 |

- **SSL Encryption and Cipher Suites** : SSL encryption and cipher suites are crucial for securing data in transit over the internet.

    A reverse proxy facilitates SSL termination, handling the encryption and decryption processes on behalf of the backend servers. This not only offloads the resource-intensive encryption tasks but also allows for centralized management of SSL configurations, ensuring a robust and secure communication channel.
    | Property                               | Description                                                                                                      |
    | -------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
    | SSL/TLS Protocol and Cipher Suites      | Use Latest TLS version. Enable perfect forward secrecy (new temporary private key per session), disable outdated and insecure protocols like SSLv2 and SSLv3. |
    | SSL Certification Configuration         | Configure SSL certificate and private key.                                                                        |
    | HSTS (HTTP Strict Transport Security) Header | To force secure connection.                                                                                       |
    | SSL HSTS Preload                        | Submit domain to HSTS preload.                                                                                    |
    | OCSP (Online Certificate Status Protocol) | For faster and reliable certificate.                                                                              |
    | SSL Session Cache and Timeout           | Decide per requirement.                                                                                           |
    | SSL Buffer Size and Timeout             | Decide per requirement.                                                                                           |
    | SSL Early Data (Optional)               | Enable SSL early data for faster page load.                                                                       |
    | SSL Termination                         | Terminate SSL at the load balancer if using one, to offload encryption/decryption from the backend server.       |
    | Cipher Suite Configuration              | Fine-tune SSL cipher.                                                                                             |

- **Caching** : Caching involves storing copies of frequently accessed data to improve response times and reduce server load.

    A reverse proxy, as a caching layer, stores static content and cached responses, delivering them directly to clients without reaching the backend servers. This accelerates content delivery, minimizes server load, and enhances overall system performance by serving precomputed or cached results when applicable.
    | Property                               | Description                                                                                                      |
    | -------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
    | Proxy Cache Path                        | Define the path where nginx will store cached files (cache directory structure, key_zone, cache max size, cache active time, disable temporary file storage for the cache.) |
    | Cache Configuration                     | Declare (proxy cache, proxy cache valid, proxy ignore headers).                                                    |
    | Cache for a Specific Location           | Use “cookie_nocache” and “arg_nocache”.                                                                           |
    | Cache Purging                           | Implement cache to manually invalidate and remove specific items for cache (e.g., For dev/qa: allow only localhost domain traffic).                                               |
    | Cache Busting                           | Implement cache busting to force cache refresh, using the last modified headers.                                  |
    | Graceful Cache Rebuilding               | Implement a mechanism to gracefully rebuild cache after it's cleared.                                              |
    | Brotli Compression and Caching          | Enable Brotli compression and cache compressed versions of assets.                                                 |
