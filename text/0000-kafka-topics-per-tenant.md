
- Start Date: 2022-08-12
- RFC PR: (leave this empty)
- FOLIO Issue: (leave this empty)

# Cease Creating Kafka Topics Per Tenant

## Summary

The purpose of this RFC is to lay out the reasoning to stop creating Kafka topics for each tenant installed into a FOLIO cluster.

## Motivation
- Increase scalability of FOLIO's event driven design when used in a Kafka installation. More partitions means more units of work by Kafka.
- Removes the ceiling on the number of tenants FOLIO can have. This will currently be impacted by soft Kafka partitions limit.
- Reduce monetary cost when using managed Kafka installation in the cloud. This promotes the success of the FOLIO platform by reducing hosting costs.
- Remove false sense of tenant isolation and incomplete implementation proposed by [Temporary Kafka Security Solution](https://wiki.folio.org/x/YYVFAw)

## Detailed Explanation/Design
FOLIO should cease creating Kafka topics for each tenant installed. This has scaling & cost implications.

FOLIO is a multi-tenant system that allows hosting providers to allocate their tenants to varying cluster configurations. Some tenants should exist on their own clusters while other tenants, with low volume, can be installed into a shared cluster. Installation of a tenant in a cluster means that topics will be created exclusively for the tenant. This approach is one part of a three part proposal to imbibe FOLIO tenant isolation in Kafka:
- Kafka topics for each tenant.
- Dedicated Kafka users for each tenant.
- ACLs limiting topic access to specific Kafka users.

More details about this proposal, [Temporary Kafka Security Solution](https://wiki.folio.org/x/YYVFAw). 

This solution is meant to be temporary as its name implies, a stop gap to a permanent solution. Creating topics for each tenant has been implemented and lightly enforced via the [folio-kafka-wrapper](https://github.com/folio-org/folio-kafka-wrapper) library. FOLIO is not able to enforce tenant security and isolation within its boundaries: the other two parts are not defined within FOLIO code, it is up to the hosting provider to implement. 

Attempting to implement the other two parts would probably force a decision to have dedicated modules instances to retain distinct credentials needed to access Kafka thereby raising FOLIO's cost-to-host. Having modules per tenant is cost prohibitive, causes inefficient use of some resources and overwhelm other resources e.g. increasing database connections for each module instance. At the time of writing, FOLIO's own release infrastructure does not have modules per tenant or Kafka security configurations as described by the Temporary Kafka Security Solution. Another consequence would be the need for a process to create Kafka users when a tenant is installed in okApi. A representation of Kafka ACLs would also need to be created for FOLIO developers to modify definitions for which modules can access any particular topic. These items are not defined by the temporary solution and is left for a hosting provider to figure out.

Similar to a database not having a theoretical limit on the number of rows/tables, Kafka does not have a limit on partition count. A major difference is that a partition is "heavier" than a row/table. Partitions have to be rebalanced, replicated with corresponding open server files for each partition. Partitions have an ongoing administrative cost while online. Creating numerous partitions like they are database rows/tables will spell disaster for a under-provisioned Kafka installation. This is why cloud providers ensure to have limits on partition counts. Provisioning a Kafka installation to support such high partition counts will be wasteful for FOLIO hosting providers. Kafka's use in FOLIO is still limited, more FOLIO modules will continue to employ event driven techniques that will cause an explosion of partitions. It is best to catch this early.

### Migration
- changes will need to be made in folio-kafka-wrapper to stop creating topics for each tenant.
- FOLIO modules will need to reference the latest version of folio-kafka-wrapper.
- During a flower release, there should be no usage occurring in a FOLIO cluster. Existing topics will be deleted. Care should be taken to ensure all messages in the topic is consumed before topic deletion.
- FOLIO modules are instantiated, new topics are created.


## Risks and Drawbacks
- FOLIO's multi-versioning scheme involving tenants using different versions of modules will cause functional issues. Multiple versions of a module will be consuming from the same topic and there are no guarantees about which module version will consume a message sourced from an incompatible version.
- Insights like "how many messages have yet to be processed for a tenant" will be harder to derive.
- Per tenant topic settings are not possible.
- Temporary Kafka solution will no longer be possible.

## Rationale and Alternatives
The main driver for this RFC is to reduce partition count within reasonable means. Ceasing to create Kafka topics for each tenant will get us to the lowest possible count. Further feature development on FOLIO can increase the partition count, this is expected.
FOLIO's multi-versioning scheme is still a gap not covered by this change. An approach could be to create topics for each module version. This will allow specific messages to be delivered to specific versions without causing a partition count explosion when a new tenant is installed. Topics belonging to older modules versions can be removed safely.

>Example:<p>
if V1 of Module A exists and V1 and V2 of Module B exists. Each will have their own versioned topics; Module A:V1, Module B:V1, Module B:V2.

There are edge cases with this approach that will have to be fleshed out. After these edge cases are covered, I believe a complex solution would have been built.
Some hosting providers do not employ FOLIO's multi-versioning too often. FOLIO clusters are usually within the scope of a flower release with an upgrade event to shift to another flower release. Nonetheless, multi-versioning is available and there are no guarantees that it will not be used.

## Unresolved Questions

- How do we ensure tenant data security?
