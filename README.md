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

3.  **Ensure `kong.yml` is in Git**:
    - We are mounting `kong.yml` directly from the repository.
    - **CRITICAL**: You MUST ensure `kong.yml` is pushed to your GitHub/GitLab repo.
    - If this file is missing from the repo, the deployment will fail with a "not a directory" error.

4.  **Configure Domains in Easypanel**:
    - **Do not use port :3000 in your browser.** We removed the open ports for security.
    - Instead, use Easypanel's **Domains** tab to map URLs to internal ports:

    | Service | Domain Example | Port to Map |
    | :--- | :--- | :--- |
    | **Studio** | `multiple-project-supabase.4m8bfa.easypanel.host` | **3000** |
    | **API** | `api-multiple-project-supabase.4m8bfa.easypanel.host` | **8000** |

    - *Note: You can create the "api-" subdomain by just typing it in the Domains tab in Easypanel.*

5.  **Update Environment Variables**:
    - Update `API_EXTERNAL_URL` in your Easypanel Environment Variables to match your API domain.
    - **IMPORTANT**: Do NOT include `:8000` if you are using a subdomain like `api-...`. The subdomain handles the port for you.
    - Correct: `API_EXTERNAL_URL=https://api-multiple-project-supabase.4m8bfa.easypanel.host`
    - Incorrect: `API_EXTERNAL_URL=https://api-multiple-project-supabase.4m8bfa.easypanel.host:8000`


5.  **Deploy**:
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

### Error: `password authentication failed for user "postgres"`

This happens if you deployed the database *before* setting the correct `POSTGRES_PASSWORD`, or if you changed the password after the database was created. The database saves the *first* password it sees and ignores future changes.

**Solution:**
1.  **Destroy the App**: Since this is a fresh setup, the easiest fix is to delete the application in Easypanel.
2.  **Re-deploy**: Create the app again. This creates a fresh database volume that will accept your new `POSTGRES_PASSWORD`.

### Error: Persistent `password authentication failed`

If you have deleted and recreated the app and it *still* fails:

1.  **Check Password Complexity**:
    - Ensure your `POSTGRES_PASSWORD` in `.env` contains **only letters and numbers**.
    - Avoid symbols like `$`, `#`, `@`, `!` as they can confuse Docker.
    - Try a simple password like `SupabasePass123` temporarily.

2.  **Verify Volume Deletion**:
    - When you delete the app in Easypanel, ensure the **Volumes** are also deleted.
    - If the volume persists, the old password persists.

