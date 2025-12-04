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

## Troubleshooting

### Error: `mount ... not a directory` or `Are you trying to mount a directory onto a file?`

This error means Docker cannot find `kong.yml` in your repository, so it created a directory instead.

**Solution:**
1.  **Check your Git Repo**: Ensure `kong.yml` is actually present in your GitHub/GitLab repository.
2.  **Check File Location**: Ensure `kong.yml` is in the **same folder** as `docker-compose.yml`.
3.  **Alternative Fix (Easypanel Mounts)**:
    - If the Git file method fails, you can manually create the config in Easypanel.
    - Go to **Mounts** in your Easypanel service.
    - Add a **File** mount.
    - **Mount Path**: `/kong/declarative.yml`
    - **Content**: Copy and paste the content of `kong.yml` here.
    - Remove the `volumes` section for `kong` in `docker-compose.yml` if you do this.

