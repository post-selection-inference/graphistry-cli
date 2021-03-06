# Security Hardening

Graphistry instances come with security out-of-the-box, and are typically further secured by system administrators or Graphistry staff and through appropriate use. Different configurations make sense based on deployment environment, intended use cases, and threat model.

Read on for:

* Out-of-the-box config (Admin)
* Recommended network config (Admin)
* Connector configuration (Admin)
* Safely using Graphistry APIs (Developer)

For futher information, see:

* [Overall system configuration](configure.md)
* [Security threat model](threatmodel.md)
* [Deployment planning](deployment-planning.md)
* [Capacity planning](../hardware-software.md)


## Out-of-the-box config

* First user to create an account is the Admin
* Admins then create other users and with differing role levels: Admin, User, Inactive
* Sessions are protected with JWT tokens - TLS recommended
* Visualizations are shared as web keys

## Recommended network config: TLS, IPs, Ports

### TLS Certificates

We **highly** encourage using TLS and make it easy:

* [Configure the Caddyfile](configure.md) for auto-TLS with one line (recommended) or add your own TLS certificate
* TLS is required for JWT auth to be secured against MitM attacks
* Auto-TLS requires a domain name

### Firewalls & SSH

We recommend secure use of SSH and to consider using a firewall for VPN-only traffic. (Graphistry runs fine air-gapped as well.) If you do not have access to a firewall but want IP filtering, contact Graphistry staff for alternatives.

The below should be standard for cloud and enterprise environments:

* SSH: Keys for admins
* Server firewall - Inbound recommendations:
  * VPN-only if publishing is not meant for the general web
  * Port 22 (ssh): Always on, or manually enabled during administration
  * Port 443 (https): Always on
  * Port 80 (http): Always on
  * If the GPU/CPU is reused for other applications, whatever ports those systems need (we like to preassign a limited safe range)
* Server firewall - Outbound port recommendations:
  * VPN user browsers (if no general web publishing)
  * Whatever database and API systems (servers and cloud regions)

# Connector configuration

Connectors are used by the (currently) admin-level investigator tool. Security configuration is generally relevant for enabling it to work with outside systems, and to restrict what users can do.

[See the main connector docs](configure.md) for more information.

### Connector firewall settings
* If Graphistry is connecting to a database or API such as Splunk or Neo4j, ensure that system allows incoming/outgoing connections from Graphistry
* Some systems have hard-to-work-with firewalls and may benefit from using a [Graphistry data bridge](bridge.md) to limit database communications to outbound-only HTTP/HTTPS.

### Restricting connectors

Check if your database or API supports restricted roles. For example, when Graphistry is connected to Splunk, you may want to create a Splunk API user that only has read access to a few indexes, and configure Graphistry to use that account.

# Safely using Graphistry APIs


* Safe defaults
  * Ensure TLS is configured, especially for data uploads
  * Prefer the 2.0 API over the 1.0 where available
* Principle of least privilege: API tokens act on behalf of the owning account, so be as minimal as needed
  * Prefer User accounts over Admin accounts
  * Prefer individual accounts over shared accounts
  * See the API docs for more on delegation scenarios such as for revocing satellite system capabilities
* Pay attention to data movement to improve performance and limit attack opportunities:
  * Prefer server<>server APIs for data uploads
  * Prefer client<>client APIs for user interactions