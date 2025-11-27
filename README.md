# Load Balancer solution using Apache

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


## Configure Apache As A Load Balancer
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

