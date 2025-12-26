# Unraid App Templates

Custom Unraid Community Applications templates for personal projects.

## Installation

1. In Unraid, go to **Apps** > **Settings** (gear icon)
2. Add this repository URL to **Template Repositories**:
   ```
   https://github.com/hoppers99/unraid-apps
   ```
3. Click **Save** and the apps will appear in the Apps tab

## Available Apps

### Alexandria

A digital library management system for ebooks, audiobooks, and other media.

**Installation order:**

1. **PostgreSQL** (from Community Apps)
   - Install any PostgreSQL container (e.g., `postgres:17-alpine`)
   - Set `POSTGRES_DB=alexandria`, `POSTGRES_USER=alexandria`
   - Set a secure password and note it for step 2

2. **alexandria-backend**
   - Configure the database connection to match your PostgreSQL container
   - Set your library path (e.g., `/mnt/user/media/books/`)

3. **alexandria** (frontend)
   - Set the Origin URL to your Unraid IP (e.g., `http://192.168.1.100:3000`)
   - Access the web UI at the configured port

## Template Development

Templates follow the [Unraid Docker Template Schema](https://selfhosters.net/docker/templating/templating/).

To test templates locally before publishing:
1. Copy the XML file to `/boot/config/plugins/community.applications/private/`
2. Refresh Community Applications
