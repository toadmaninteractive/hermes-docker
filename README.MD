# Hermes Docker Deployment

A Docker-based deployment for the Hermes backend and frontend applications.

## Quickstart

Follow these steps to set up and run Hermes using Docker:

### 1. Clone Hermes repos

Create a folder for Hermes-related repos:

```bash
mkdir hermes && cd hermes
```

Clone Hermes repos:

```bash
git clone https://github.com/toadmaninteractive/hermes-backend
git clone https://github.com/toadmaninteractive/hermes-frontend
git clone https://github.com/toadmaninteractive/hermes-docker
```

### 2. Build Hermes backend image

Open `hermes-backend` dir and run:

```bash
docker build -t hermes-backend:latest .
```

### 3. Build Hermes frontend image

Open `hermes-frontend` dir and run:

```bash
docker build -t hermes-frontend:latest --build-arg BUILD_ENV=production .
```

### 4. Create dedicated Docker network

```bash
docker network create hermes
```

### 5. Configure Hermes

Open `hermes-docker` dir and create a configuration file from an example:

```bash
cp config.yaml.example config.yaml
```

Edit the config.yaml file:
- Fill in your LDAP settings (LDAP server address and bind user CN / password)
- Set `admin_group` to the correct group name containing users who will be Hermes administrators

### 6. Run Docker Compose

```bash
docker compose up -d
```

### 7. Prepare database

Connect to the Hermes backend interactive console:

```bash
docker compose exec hermes-backend /app/bin/server remote --sname remote --cookie hcookie --remsh hermes_local
```

In the console, type the following commands:

```elixir
Repo.Seeds.run(:prod)
Hermes.sync_ldap()
```

Exit the console by pressing `Ctrl+G`, then `Q`, and finally `Enter`.

## Accessing Hermes in a browser

After completing the Quickstart guide, Hermes will be running in Docker containers.
To access the web interface through a browser, you'll need to set up a reverse proxy.

We provide two recommended approaches:

### Option 1: Local Proxy Setup
Configure a local proxy server such as **nginx** to route traffic to the Hermes containers.

### Option 2: Traefik Proxy (Recommended for Production)
We provide a pre-configured [Traefik](https://github.com/traefik/traefik) setup for production deployments.
Traefik automatically handles SSL termination, load balancing, and routing.

#### Prerequisites for Traefik
- **Registered domain name** pointing to your server
- **SSL certificates** (certificate chain and private key)

#### Traefik Configuration Steps

1. **Prepare SSL certificates**
- Save your certificate chain as `cert_key_chain.crt`
- Save your private key as `private.key`
- Place both files in the traefik/certs directory

2. **Configure your domain**
- Edit `traefik/conf/dynamic/hermes.yml`
- Replace `hermes.yourcompany.com` with your actual domain name

3. **Start Traefik**

Run this command from the `docker` directory:

```bash
docker compose up -d
```

4. **Stop Traefik**

Run this command from the `docker` directory:

```bash
docker compose down
```

#### Verification
After starting Traefik, access Hermes at https://your-domain.com (replace with your actual domain).
The proxy automatically handles SSL encryption and redirects HTTP traffic to HTTPS.

#### Additional notes
- Ensure your firewall allows traffic on ports 80 (HTTP) and 443 (HTTPS)
- DNS records should point to your server's public IP address
- Certificate files must be in PEM format
- Traefik automatically monitors container changes and updates routing accordingly
