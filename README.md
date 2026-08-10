# DNS Spoofer

[![CI](https://github.com/Vessel9817/dns-spoofer/actions/workflows/ci.yml/badge.svg)](https://github.com/Vessel9817/dns-spoofer/actions/workflows/ci.yml)

This project is a practical demo of DNS spoofing, which allows you to perform
man-in-the-middle (MITM) attacks on users of this service. This bypasses some
security measures, such as certain HTTPS protections.

**This is a tool that should be used responsibly.**

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

- Expose a certificate authority (CA) for applications to query.
  Though, spoofing may be possible.
- Rewrite headers, such as Location or Content-Security-Policy
- Rewrite HTML, such as absolute URLs
- Bypass HSTS. Though spoofing Google's [HSTS preload][hsts-preload] service
  could certainly give the impression of doing so.
- Support mDNS, though it remains untested
- Perform HTTPS downgrading attacks
- Prevent applications, such as browsers, from reaching other DNS services
- Bypass DNSSEC

This project cannot:

- Fool applications with an embedded public key. You require the domain's private key to generate a verifiable certificate.

[hsts-preload]: https://hstspreload.org/
