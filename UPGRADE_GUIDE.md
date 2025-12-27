# Upgrading Alexandria on Unraid

## From v0.1.0 to v1.0.0 (Authentication Update)

### What's New

Version 1.0.0 adds:
- Multi-user authentication with sessions
- Auto-admin creation from environment variables
- Automatic database initialization and migrations
- Role-based access control
- Database backup/restore tools

### Required Actions

⚠️ **You MUST add the Secret Key variable before upgrading!**

### Step-by-Step Upgrade

#### 1. Backup (Recommended)

```bash
# Access Unraid console and run:
docker exec alexandria-backend uv run librarian backup /mnt/user/appdata/alexandria/backups/pre_v1.0.0.dump

# Or via Unraid terminal:
# Navigate to Console for alexandria-backend container
# Run: uv run librarian backup /backups/pre_v1.0.0.dump
```

#### 2. Stop Container

In Unraid:
- Go to Docker tab
- Click on alexandria-backend
- Click "Stop"

#### 3. Update Template

Click "Edit" on alexandria-backend container:

**Add these NEW REQUIRED variables:**

| Variable | Value | Notes |
|----------|-------|-------|
| ALEXANDRIA_SECRET_KEY | `[Generate random string]` | **CRITICAL!** Generate with `openssl rand -base64 32` |
| ALEXANDRIA_ADMIN_USERNAME | `admin` | Only used if no users exist |
| ALEXANDRIA_ADMIN_PASSWORD | `[Your password]` | Only used if no users exist |

**How to generate SECRET_KEY:**
- Option 1: Use Unraid terminal: `openssl rand -base64 32`
- Option 2: Use password manager to generate 32+ character random string
- Option 3: Use online generator (not recommended for production)

**Optional variables:**

| Variable | Default | Description |
|----------|---------|-------------|
| ALEXANDRIA_ADMIN_EMAIL | (none) | Admin email address |
| ALEXANDRIA_SESSION_EXPIRE_MINUTES | 10080 | Session lifetime (1 week) |
| ALEXANDRIA_ENABLE_REGISTRATION | false | Allow self-registration |
| ALEXANDRIA_GUEST_ACCESS | false | Allow unauthenticated browsing |

#### 4. Update Container Image

```bash
# In Unraid Docker tab:
# Click "Check for Updates"
# If update available, click "Update"

# Or manually:
# Click "Force Update" on alexandria-backend
```

#### 5. Start Container

- Click "Start" on alexandria-backend
- Watch logs for successful migration

#### 6. Verify Upgrade

Check container logs (click "Logs" button):

You should see:
```
App version: 0.2.0
Current schema version: 75b0e362b398
Required schema version: b2640a924996
Running upgrade 75b0e362b398 -> b2640a924996, add_sessions_table
✓ Database migrations completed successfully
✓ Seeded 1000 classification codes
```

**Important:** You should NOT see "Created initial admin user" because you already have users.

#### 7. Test Login

- Access web UI (http://[unraid-ip]:3000)
- Login with your EXISTING credentials
- Your old account should work normally

### Troubleshooting

#### "No users exist" message in logs

If you see "Created initial admin user" but you had existing users:

**Cause:** Database was reset or migrations failed

**Solution:**
1. Stop container
2. Restore backup:
   ```bash
   docker exec alexandria-backend uv run librarian restore /backups/pre_v1.0.0.dump
   ```
3. Check logs for migration errors
4. Restart container

#### Can't login with old password

**Cause:** This shouldn't happen - old users are preserved

**Solution:**
1. Check logs for errors
2. Verify you're using correct username
3. Create new admin if needed:
   ```bash
   docker exec -it alexandria-backend uv run librarian create-admin
   ```

#### "Secret key not set" error

**Cause:** ALEXANDRIA_SECRET_KEY not configured

**Solution:**
1. Stop container
2. Edit template
3. Add ALEXANDRIA_SECRET_KEY variable
4. Generate value: `openssl rand -base64 32`
5. Start container

#### Database migration failed

**Symptoms:**
- Container starts but API errors
- Logs show migration errors

**Solution:**
```bash
# Stop container
docker stop alexandria-backend

# Manual migration (from Unraid console)
docker exec alexandria-backend uv run alembic upgrade head

# Restart container
docker start alexandria-backend
```

### Fresh Install (New Users)

If you're installing Alexandria for the first time:

1. Install PostgreSQL from Community Apps
2. Configure PostgreSQL:
   - POSTGRES_DB=alexandria
   - POSTGRES_USER=alexandria
   - POSTGRES_PASSWORD=[secure password]
3. Install alexandria-backend
4. Configure required variables:
   - Database connection (use container name for host)
   - ALEXANDRIA_ADMIN_USERNAME=admin
   - ALEXANDRIA_ADMIN_PASSWORD=[secure password]
   - ALEXANDRIA_SECRET_KEY=[generate random]
5. Start container
6. Database auto-initializes!
7. Login with admin credentials
8. Install alexandria (frontend)

### Rollback Procedure

If you need to rollback to v1.0:

```bash
# 1. Stop container
docker stop alexandria-backend

# 2. Restore pre-upgrade backup
docker exec alexandria-backend uv run librarian restore /backups/pre_v1.0.0.dump

# 3. Edit template - use old version tag
# Change Repository to: ghcr.io/hoppers99/alexandria-backend:1.0.0

# 4. Force update to download old version
# Click "Force Update" in Unraid

# 5. Start container
docker start alexandria-backend
```

⚠️ **Warning:** Rollback will lose any data created after upgrade (new users, sessions, etc.)

### Post-Upgrade Checklist

After successful upgrade:

- [ ] Can login with existing credentials
- [ ] Web UI loads correctly
- [ ] Books and collections intact
- [ ] Reading progress preserved
- [ ] Backend logs show no errors
- [ ] Frontend connects to backend
- [ ] Create backup of new version

### Need Help?

- GitHub Issues: https://github.com/hoppers99/alexandria/issues
- Check logs: Click "Logs" in Unraid Docker tab
- Verify database: `docker exec alexandria-postgres psql -U alexandria -d alexandria -c "SELECT * FROM alembic_version;"`

### Future Upgrades

With v2.0+, future upgrades are automatic:

1. Click "Check for Updates" in Unraid
2. Click "Update" if available
3. Database migrates automatically
4. No manual steps needed!

Schema versioning is now tracked, and migrations run automatically on startup.
