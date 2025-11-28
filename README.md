# ⚖️ Load Balancer solution using Apache

Load balancing is an architectural strategy that distributes incoming application or website traffic across multiple backend servers to ensure high availability, scalability, and fault tolerance ⚙️. Instead of relying on a single server, a load-balanced environment uses a dedicated component—called a load balancer—as the unified entry point for users 🎯.

The load balancer evaluates server health and workload, then routes client requests to the most appropriate server, preventing overload, reducing latency, and improving overall system reliability 🚦. This approach enables organizations to scale vertically by adding more resources to one server, or scale horizontally by adding more servers as demand grows, delivering a more flexible and resilient infrastructure 🏗️.

This guide provides a step-by-step approach to deploying and configuring an Apache Load Balancer to enhance the scalability and reliability of the Tooling Website solution 🚀.

<img width="965" height="558" alt="image" src="https://github.com/user-attachments/assets/5e84f9a5-b86f-4e03-bc94-2988ea159015" />


## Prerequisites
Make sure that you have the following servers installed and configured:

- Two RHEL9 Web Servers
- One MySQL DB Server (based on Ubuntu 24.04)
- One RHEL9 NFS server

<img width="977" height="471" alt="image" src="https://github.com/user-attachments/assets/06036db1-5dc2-4e27-b2de-101e651467c8" />


## Step 1: Configure Apache As A Load Balancer
- Create an Ubuntu Server EC2 instance and name it `Project-8-apache-lb`

- Open TCP port 80 on `Project-8-apache-lb` by creating an Inbound Rule in Security Group.

- Install Apache Load Balancer on `Project-8-apache-lb` server and configure it to point traffic coming to LB to both Web Servers:

## Install Apache and its dependencies:

```
sudo apt update && sudo apt upgrade -y

sudo apt install apache2 -y

sudo apt-get install libxml2-de
```

## Enable the following Modules:

```
sudo a2enmod rewrite

sudo a2enmod proxy

sudo a2enmod proxy_balancer

sudo a2enmod proxy_http

sudo a2enmod headers

sudo a2enmod lbmethod_bytraffic
```
## Restart apache2 service:

```
sudo systemctl restart apache2

sudo systemctl status apache2
```

## Configure Load Balancing

### Edit the Apache configuration file:

```
sudo vi /etc/apache2/sites-available/000-default.conf
```

- Add this configuration into this section `<VirtualHost *:80>  </VirtualHost>`

```
<Proxy "balancer://mycluster">
    BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
    BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
    ProxySet lbmethod=bytraffic
    # ProxySet lbmethod=byrequests
</Proxy>

ProxyPreserveHost on
ProxyPass / balancer://mycluster/
ProxyPassReverse / balancer://mycluster/
```

> This setup uses the by-traffic load-balancing approach, where incoming requests are routed to web servers based on how busy they currently are. The loadfactor setting defines the share of traffic each server should receive.

- Restart the Apache server:

```
sudo systemctl restart apache2
```

- Test load balancer via web browser using the LB's public IP address:

```
http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php
```

- Open two SSH sessions—one for each Web Server—and unmount /var/log/httpd/ on both servers (if it is currently mounted).

```
df -h

sudo systemctl stop httpd

sudo umount /var/log/httpd

sudo systemctl restart httpd

sudo systemctl status httpd
```

- Run this command on each Web Server:

```
sudo tail -f /var/log/httpd/access_log
```

> Reload the load balancer page several times. Each refresh should generate new log entries on both web servers, and the request counts should be similar since both have identical load factors.

---

## Step 2: Configure IP-to-Domain Name Mapping

- Open this file on your Load Balancer server:

```
sudo vi /etc/hosts
```

- Insert the following entries into the file to map each server’s IP address to an arbitrary hostname.

```
<WebServer1-Private-IP-Address> Web1
<WebServer2-Private-IP-Address> Web2
```

- Update the load balancer configuration file to use these server names.

```
sudo vi /etc/apache2/sites-available/000-default.conf
```

- Use the arbitrary names in place of the IP addresses.

```
BalancerMember http://Web1:80 loadfactor=5 timeout=1
BalancerMember http://Web2:80 loadfactor=5 timeout=1
```

- Test the configuration by using curl from the load balancer to access each web server.

```
curl http://Web1

curl http://Web2
```
---

### Congratulations! Your DevOps team now has a fully operational load-balanced web environment, enhancing performance and reliability.
