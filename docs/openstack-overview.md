# Building the cloud

From this point forward we're building our OpenStack cloud. The following commands will leverage `helm` as the package manager and `kustomize` as our configuration management backend.

## Deployment choices

When you're building the cloud, you have a couple of deployment choices, the most fundamental of which is `base` or `aio`.

* `base` creates a production-ready environment that ensures an HA system is deployed across the hardware available in your cloud.
* `aio` creates a minimal cloud environment which is suitable for test, which may have low resources.

The following examples all assume the use of a production environment, however, if you change `base` to `aio`, the deployment footprint will be changed for a given service.

## The DNA of our services

The DNA of the OpenStack services has been built to scale, and be managed in a pseudo light-outs environment. We're aiming to empower operators to do more, simply and easily. Here are the high-level talking points about the way we've structured our applications.

* All services make use of our core infrastructure which is all managed by operators.
* Backups, rollbacks, and package management all built into our applications delivery.
* Databases, users, and grants are all run against a MariaDB Galera cluster which is setup for OpenStack to use a single right, and read from many.
  * The primary node is part of application service discovery and will be automatically promoted / demoted within the cluster as needed.
* Queues, permissions, vhosts, and users are all backed by a RabbitMQ cluster with automatic failover. All of the queues deployed in the environment are done with Quorum queues, giving us a best of bread queing platform which gracefully recovers from faults while maintaining performance.
* Horizontal scaling groups have been applied to all of our services. This means we'll be able to auto scale API applications up and down based on the needs of the environment.