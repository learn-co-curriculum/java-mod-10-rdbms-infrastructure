# RDBMS Infrastructure

## Learning Goals

- Understanding the different components that comprise a complex Database environment

## Introduction

When designing and writing software applications, Database systems can frequently be though of as just dumb stores for data. For more complex use cases, their intrinsic search and query abilities can be used as a core
components of business logic.
Either way, they tend to be handled as individual concrete systems. So, it might come as a surprise that database environments can be just as complex as any highly scaleable and distributed microservice application.

> Note: An example of a recommended architecture setup from a leading managed Postgres vendor  
> We can see here a single point of entry into the Database system. Beyond that point there are multiple different components comprising what may be considered a single database.

![https://www.enterprisedb.com/docs/pgd/latest/overview/harp/02_overview/](https://www.enterprisedb.com/docs/static/559d896a828c3b7766d48bf555fc0690/a6d36/bdr-ao-spec.png)


## Different components of a Database Environments

Here is a general breakdown of how Database environments tend to be implemented. This is by no means a complete description, as there is still ongoing development in this space.

### Font-End Load Balancer/Proxy

The first component a request will pass though will be some type of query proxy. The purpose of this component might be to load balance queries to multiple backends, pool client connections together to reduce session load on the servers,
or for smarter routing of queries to the more complex infrastructure using a single point of entry for a client.

A common Open Source project that fits this role is Pgpool-II. Take a look at the intro documentation to see a good description of its use.

- [Pgpool-II Intro](https://www.pgpool.net/docs/37/en/html/intro-whatis.html)

### Multiple Database Servers

What a client might see as a single Database Server could end up being any amount of backend servers placed behind a proxy. Whereas web applications are easily scaled in this manner,
stateful applications like Database systems need to synchronize this data between all the independent servers. This is not a trivial task,
and the difficulties in solving this can be attributed as a leading cause towards the development of NoSQL systems.

The most common and well understood scaling mode is with a single Master node capable of Reads and Writes, and any amount of Replica nodes capable of Reads Only.
This leads to situations where Writes are still restricted to a single node, but Reads can be scaled essentially infinitely to support applications that are heavy Read based.

There are many other modes to support other use cases, but they tend to be more complex.

- [High Availability](https://www.postgresql.org/docs/current/high-availability.html)
- [Different Scaling Solutions](https://www.postgresql.org/docs/current/different-replication-solutions.html)

### Failover

The problem with most of the above solutions is that the database nodes are split between handling different roles. Any client has to be aware of these roles to interact appropriately.
In the event of system failure, the state of a distributed cluster needs to be altered and updated among all the participants.

- [Failover](https://www.postgresql.org/docs/current/warm-standby-failover.html)

### Backups

Backups are probably the most important need of a Database environment from the Operations perspective, but tends to be the lowest priority from the Developer perspective. At least, until data loss occurs.
If data in a Database system isn't being considered temporary, backups and successful restore processes are absolutely required before an environment should ever be considered Production quality.

- [Backups](https://www.postgresql.org/docs/current/backup.html)
