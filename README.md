# PostgreSQL High-Availability Cluster :elephant: :sparkling_heart:

# Forked and used to deploy CPSQL, CPSQL1, and CPSQL2

See original README.md in history. Project was renamed to Autobase shortly after our deployment.

---

### Production-ready PostgreSQL High-Availability Cluster (based on Patroni). Automating with Ansible.

`postgresql_cluster` automates the deployment and management of highly available PostgreSQL clusters in production environments. This solution is tailored for use on dedicated physical servers, virtual machines, and within both on-premises and cloud-based infrastructures.

You can find a version of this documentation that is searchable and also easier to navigate at [postgresql-cluster.org](http://postgresql-cluster.org)

---

###

![postgresql_cluster](images/postgresql_cluster.png#gh-light-mode-only)
![postgresql_cluster](images/postgresql_cluster.dark_mode.png#gh-dark-mode-only)


#### PostgreSQL High-Availability with Load Balancing

This scheme enables load distribution for read operations and also allows for scaling out the cluster with read-only replicas.

When deploying to cloud providers such as AWS, GCP, Azure, DigitalOcean, and Hetzner Cloud, a cloud load balancer is automatically created by default to provide a single entry point to the database (controlled by the `cloud_load_balancer` variable).

For non-cloud environments, such as when deploying on Your Own Machines, the HAProxy load balancer is available for use. To enable it, set `with_haproxy_load_balancing: true` in the vars/main.yml file.

> [!NOTE]
> Your application must have support sending read requests to a custom port 5001, and write requests to port 5000.

List of ports when using HAProxy:
- port 5000 (read / write) master
- port 5001 (read only) all replicas
- port 5002 (read only) synchronous replica only
- port 5003 (read only) asynchronous replicas only

##### Components of HAProxy load balancing:

- [**HAProxy**](http://www.haproxy.org/) is a free, very fast and reliable solution offering high availability, load balancing, and proxying for TCP and HTTP-based applications. 

- [**confd**](https://github.com/kelseyhightower/confd) manage local application configuration files using templates and data from etcd or consul. Used to automate HAProxy configuration file management.

- [**Keepalived**](https://github.com/acassen/keepalived)  (_optional, if the `cluster_vip` variable is specified_) provides a virtual high-available IP address (VIP) and single entry point for databases access.
Implementing VRRP (Virtual Router Redundancy Protocol) for Linux. In our configuration keepalived checks the status of the HAProxy service and in case of a failure delegates the VIP to another server in the cluster.
