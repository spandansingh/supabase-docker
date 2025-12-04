# Supabase Self-Hosting on Hostinger VPS / Easypanel

This repository contains the configuration files to run a full Supabase stack on Hostinger VPS using Easypanel.

## Structure

- `docker-compose.yml`: Defines all Supabase services (Studio, Kong, Auth, Rest, Realtime, Storage, Postgres).
- `kong.yml`: Configuration for the Kong API gateway to route requests to the correct services.
- `.env.example`: Template for environment variables.

## Setup Instructions

1.  **Deploy to Easypanel**:
    - Go to your Easypanel dashboard.
    - Create a **New App**.
    - Select **Docker Compose**.
    - Choose **From Git Repo**.
    - Paste the URL of this repository.

2.  **Configure Environment Variables**:
    - In the Easypanel setup screen, you will need to provide the following environment variables.
    - A `.env` file has been generated for you locally with secure random keys. **Copy the values from that file.**

    **Required Variables:**
    - `POSTGRES_PASSWORD`
    - `JWT_SECRET`
    - `SECRET_KEY_BASE`
    - `ANON_KEY`
    - `SERVICE_ROLE_KEY`
    - `API_EXTERNAL_URL` (Set this to `http://YOUR_VPS_IP:8000`)

3.  **Deploy**:
    - Click **Create** or **Deploy**.

## Accessing Services

Once deployed, your services will be available at:

- **Supabase Studio**: `http://YOUR_VPS_IP:3001`
- **API Gateway**: `http://YOUR_VPS_IP:8000`
    - Auth: `/auth`
    - Rest: `/rest`
    - Realtime: `/realtime`
    - Storage: `/storage`
