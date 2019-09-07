# Planning a Graphistry Deployment

You can architect your deployment based on usage mode and environment constraints. We recommend starting with AWS/Azure Marketplace for most projects.

The following documents describe **common Graphistry configurations**, examples of **which configuration may make sense for you**, and **Graphistry's components**.

Related:

* **Capacity planning and host environment configuration**: 
  <br>See [Recommended Deployment Configurations: Client, Server Software, Server Hardware](../hardware-software.md) 
* **Graphistry server configuration**:
  <br>See [main configuration docs](configure.md)



## 1. Common Configurations  

Graphistry deployments generally follow one of these patterns:


1. **Preconfigured AWS/Azure Marketplace instance**
<br/>The most typical case.
  * Preconfigured for one-click quicklaunch: Nvidia, Docker, Graphistry, Jupyter, connectors, and many DB/API samples
  * Data stays in your team's private cloud account
  * Great for POCs. Utility-fee pricing means cheap POCs by turning off the instance when inactive.
  * Works well with databases also running securely in your cloud account and external APIs
  * Dedicated Graphistry support

2. **Preconfigured AWS/Azure Marketplace instance with on-prem data bridging**
<br/>On-prem enterprise data sources can likely be accessed as-is or via firewall changes. However, sometimes it is easier to use an on-prem jump box.
  * Start with Graphistry AWS/Azure Marketplace as usual, e.g., for POCs
  * Run the Graphistry Data Bridge closer to the DB/API, e.g., on-prem
  * Configure the Graphistry Data Bridge, Graphistry server, and firewalls/DBs to work in bridged mode

3. **Manual GPU environment with Graphistry containers**
<br/>Primarily useful for heavily regulated environments such as Federal and Financial teams with requirements such as air-gapping.
  * Start with Graphistry AWS/Azure Marketplace as usual, e.g., for POCs
  * When ready, follow Graphistry guides for Nvidia/Docker environment setup and Graphistry container installation
  * Likely benefits from online Graphistry Support
  * We recommend Cloud over On-Prem when possible


4. **Graphistry Cloud (Alpha)**
<br>Graphistry, Inc. runs and manages your Graphistry account, with a choice of shared and isolated tiers:
  * Isolated tier: Useful when it is difficult to one-click launch on the Marketplace or procure/setup on-prem hardware
  * Shared tier: Useful if you want the cost savings and expect limited use


## 2. Picking a Configuration

* **POC**: 
  <br>Save significant time by going with **AWS/Azure Marketplace**!
  * Optional: Turn off Graphistry when not using it
  * Do not use if you cannot put your data in the cloud
  * Dedicated Graphistry staff is happy to assist with setup, configuration, demos, use cases, and other POC tasks
  * If needed, Graphistry staff can help with Data Bridge setup

* **Analyst team**:
  <br/>Go with **AWS/Azure Marketplace** or **AWS/Azure BYOL**
  * Do not use if you cannot put your data in the cloud
  * Dedicated Graphistry staff is happy to assist with setup, configuration, demos, use cases, and other POC tasks
  * If needed, Graphistry staff can help with Data Bridge setup

* **Analyst team, no AWS/Azure/GPU access**:
  <br/>Go with **Graphistry Cloud** (alpha). Please contact Graphistry for discussion of options and support.

* **Highly-regulated federal, bank, telco, utility, hospital**:
  <br/>Get started with **Graphistry Marketplace** for the POC, and go on-prem from there.
  
* **Commercial Product Developer**:
  <br/>To empower your product with Graphistry, we recommend POC'ing via Marketplace and using Graphistry Cloud's API (alpha) similar to how you might use Google Maps.


## 3. Graphistry Components

Understanding Graphistry's architecture may help you make your deployment architecture decisions:

* **Graphistry Core: The visualization server**
  - GPU Visualization uploads and live sessions
  - Docker containers that require nvidia-docker and Nvidial Pascal or later (See [GPU/OS hardware requirements](../hardware-software.md))
  - Supports multi-GPU (single node) and GPU resource isolation
  - Configurable reverse proxy, TLS, and user/API key management
* **Graphistry Investigation Server (Optional)**
  - Installed as part of Graphistry Core
  - Investigation templates
  - Investigation cases, with embedded visualizations/sessions
  - Connectors: Pushdown queries over DB/API connectors
  - Deep linkable: External systems (Splunk, dashboards, emails, ...) can be configured with links into Graphistry views and workflows
* **Graphistry Data Bridge (Optional)**
  - Standalone docker-based server (See [Graphistry Data Bridge](bridge.md))
  - Enables individual Investigation Server connectors to proxy through a jump server
  - Useful for bridging Graphistry cloud with on-prem data sources when simpler alternatives do not suffice
* **Graphistry API**
  - Used by external web apps, notebook servers, etc.
  - Communicates via language-neutral REST APIs and Graphistry-provided per-language convenience libraries
* **Data Science Notebooks**
  - Jupyter is installed as part of Graphistry Core
  - External notebooks may also be used via Graphistry API

# 