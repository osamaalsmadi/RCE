# Python Deserialization Security Lab

Educational Python deserialization lab with impact analysis and mitigation notes.

**Python · Flask · Docker**

## Learning focus

Unsafe deserialization and its impact on application execution.

## Repository guide

| Resource | Location |
| --- | --- |
| Assessment | [cyber2x1.1.00/pro_01/Readme.md](cyber2x1.1.00/pro_01/Readme.md) |
| Remediation | [cyber2x1.1.00/pro_01/fix.md](cyber2x1.1.00/pro_01/fix.md) |
| Compose configuration | [cyber2x1.1.00/pro_01/docker-compose.yml](cyber2x1.1.00/pro_01/docker-compose.yml) |

## Inspect the lab

1. Read the assessment and remediation notes together.
2. Inspect the Dockerfile and Compose configuration before running the application.
3. Use an isolated local environment for authorized training. The supplied Compose file publishes a host port; it is not a production deployment configuration.
4. Record observations and compare them with the mitigation notes.

## Setup requirements

Docker with Compose is required for the supplied container configuration. The lab directory is `cyber2x1.1.00/pro_01`.

Container builds and vulnerability demonstrations have not been revalidated as part of this documentation update.

The Dockerfile uses Python 3.8. Treat its runtime and unpinned dependencies as legacy lab configuration.

---

Part of [Osama Alsmadi’s project portfolio](https://github.com/osamaalsmadi).
