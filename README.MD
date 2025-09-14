# Hermes Docker Deployment

A Docker-based deployment for the Hermes backend and frontend applications.

## Quick Start

Follow these steps to set up and run Hermes using Docker:

### 1. Clone Hermes repos

Create a folder for Hermes-related repos:

```shell
mkdir hermes && cd hermes
```

Clone Hermes repos:

```shell
git clone https://github.com/toadmaninteractive/hermes-backend
git clone https://github.com/toadmaninteractive/hermes-frontend
git clone https://github.com/toadmaninteractive/hermes-docker
```

### 2. Build Hermes backend image

Open `hermes-backend` dir and run:

```shell
docker build -t hermes-backend:latest .
```

### 3. Build Hermes frontend image

Open `hermes-frontend` dir and run:

```shell
docker build -t hermes-frontend:latest --build-arg BUILD_ENV=production .
```

### 4. Create dedicated Docker network

```shell
docker network create hermes
```

### 5. Configure Hermes

Open `hermes-docker` dir and create a configuration file from an example:

```shell
cp config.yaml.example config.yaml
```

Edit the config.yaml file:
- Fill in your LDAP settings (LDAP server address and bind user CN / password)
- Set `admin_group` to the correct group name containing users who will be Hermes administrators

### 6. Run Docker Compose

```shell
docker compose up -d
```

### 7. Prepare database

Connect to the Hermes backend interactive console:

```shell
docker compose exec hermes-backend /app/bin/server remote --sname remote --cookie hcookie --remsh hermes_local
```

In the console, type the following commands:

```elixir
Repo.Seeds.run(:prod)
Hermes.sync_ldap()
```

Exit the console by pressing `Ctrl+G`, then `Q`, and finally `Enter`.
