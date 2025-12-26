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

- **Backend**: FastAPI application for API and file processing
- **Frontend**: SvelteKit web interface
- **Database**: PostgreSQL 17

## Template Development

Templates follow the [Unraid Docker Template Schema](https://selfhosters.net/docker/templating/templating/).

To test templates locally before publishing:
1. Copy the XML file to `/boot/config/plugins/community.applications/private/`
2. Refresh Community Applications
