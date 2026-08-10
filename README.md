# DNS Spoofer

[![CI](https://github.com/Vessel9817/dns-spoofer/actions/workflows/ci.yml/badge.svg)](https://github.com/Vessel9817/dns-spoofer/actions/workflows/ci.yml)

This project is a practical demo of DNS spoofing, which allows you to perform
man-in-the-middle (MITM) attacks on users of this service. This bypasses some
security measures, such as certain HTTPS protections.

**This is a tool that should be used responsibly and ethically.**

## Installation

- Install Docker Desktop
- Install WireGuard
- Rename [.env.example](./.env.example) to `.env` and modify its values:

  - Set `WG_HOST` to the output of the following command:

    ```shell
    # Linux
    hostname -I

    # Windows
    wsl -- hostname -I
    ```

    This will tell the VPN to relay connections from your device.

  - Set `PASSWORD_HASH` to the output of the following command:

    ```shell
    docker run --rm ghcr.io/wg-easy/wg-easy:14 wgpw 'WhateverPasswordYouWant'
    ```

    Where `WhateverPasswordYouWant` can be any password of your choosing.
    This is the password used in the example config, so it's strongly advised.
    to change it. Make sure to escape shell characters appropriately.
    This is the password to the VPN admin portal.

- [Configure](#configuration) the project to your liking
- Start the project by running the following command in the project root:

  ```shell
  docker compose up -d
  ```

- Visit the VPN admin portal at: `localhost:51821`
- Log in using the previously set password
- Click "Add a new client"
- Click "Download Configuration" and save the configuration file somewhere safe.
- Open WireGuard
- Click "Import tunnel from configuration file"
- Click "Activate tunnel"

You're now connected to your DNS spoofing service.

## Usage

### Start

Assuming you've completed the [initial configuration](#installation),
start the containers:

```shell
docker compose up -d
```

Then, open WireGuard and connect to your VPN.

### Stop

Disconnect from the VPN via WireGuard. Then, stop the containers:

```shell
docker compose down
```

## Configuration

For configuring your own domains to MITM, see:
[docker-compose.yml](./docker-compose.yml)

## Advanced

Additionally, it's possible to manage individual proxy services, as defined in
[docker-compose.yml](./docker-compose.yml):

```shell
# Start
docker compose up -d <service_name_1> <service_name_2> <...>

# Stop
docker compose down <service_name_1> <service_name_2> <...>
```

## Limitations

This project currently doesn't:

- Expose a [certificate authority (CA)][ca] for applications to query.
  Though, spoofing may be possible.
- Support [ARP spoofing][arp-spoof]
- Rewrite [headers][headers], such as [`Location`][location]
  or [`Content-Security-Policy`][csp]
- Rewrite HTML, such as absolute URLs or [`base` elements][base-element].
- Bypass [HSTS][hsts]. Though, spoofing Google's [HSTS preload][hsts-preload]
  service could certainly give the impression of doing so.
- Support [mDNS][mdns]. Though, it remains untested.
- Perform HTTPS [downgrading attacks][downgrade]
- Prevent applications from reaching other [DNS][dns] servers
- Bypass [DNSSEC][dnssec]
- Bypass [mTLS][mtls]
- Intercept `.onion` domains. The entirely volunteer-driven
  [Tor network][tor-design] is designed to be decentralized and anonymous. Its
  design is highly hardened against malicious actors in any area of the network,
  making it suitable for [circumventing censorship][bridges]. The complexity
  involved in such a specialized attack is outside this project's scope.
  The dark web- and especially the deep web- as a whole should be similarly
  defensible.

This project cannot:

- Fool applications with an embedded public key. The domain's private key
  is required to generate a verifiable certificate.
- Listen in on end-to-end encrypted communication channels.
  [Diffie-Hellman key exchange][diffie-hellman] and its post-quantum successor
  [PQXDH][pqxdh] make this impossible.

[ca]: https://www.digicert.com/blog/what-is-a-certificate-authority
[arp-spoof]: https://www.crowdstrike.com/en-us/cybersecurity-101/social-engineering/arp-spoofing/
[headers]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers
[location]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Location
[csp]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Content-Security-Policy
[base-element]: https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/base#href
[hsts]: https://developer.mozilla.org/en-US/docs/Glossary/HSTS
[hsts-preload]: https://hstspreload.org/
[mdns]: https://wikipedia.org/wiki/Multicast_DNS
[downgrade]: https://www.crowdstrike.com/en-us/cybersecurity-101/cyberattacks/downgrade-attack/
[dns]: https://www.cloudflare.com/learning/dns/what-is-dns/
[dnssec]: https://www.cloudflare.com/learning/dns/dnssec/how-dnssec-works/
[mtls]: https://www.cloudflare.com/learning/access-management/what-is-mutual-tls/
[diffie-hellman]: https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange
[pqxdh]: https://signal.org/docs/specifications/pqxdh/
[tor-design]: https://github.com/Attacks-on-Tor/Attacks-on-Tor
[bridges]: https://support.torproject.org/relays/getting-started/what-is-a-bridge/
