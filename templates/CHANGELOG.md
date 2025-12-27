# Alexandria Unraid Template Changelog

## 2025-12-27 - Authentication & Auto-Initialization Update

### alexandria-backend.xml - BREAKING CHANGES

**New Required Variables:**
- `ALEXANDRIA_ADMIN_USERNAME` - Admin username for auto-creation (default: `admin`)
- `ALEXANDRIA_ADMIN_PASSWORD` - Admin password for auto-creation (REQUIRED, no default)
- `ALEXANDRIA_SECRET_KEY` - Session encryption key (REQUIRED, no default)

**New Optional Variables:**
- `ALEXANDRIA_ADMIN_EMAIL` - Admin email (optional)
- `ALEXANDRIA_SESSION_EXPIRE_MINUTES` - Session lifetime (default: 10080 = 1 week)
- `ALEXANDRIA_ENABLE_REGISTRATION` - Allow self-registration (default: false)
- `ALEXANDRIA_GUEST_ACCESS` - Allow unauthenticated access (default: false)

**Important Notes for Existing Users:**

If you're upgrading an existing installation:

1. **Add the new required variables** via Unraid template editor:
   - `ALEXANDRIA_SECRET_KEY`: Generate with `openssl rand -base64 32` or use a password manager
   - These variables are required even if you already have users in the database

2. **Database will auto-upgrade** on next container start:
   - No manual migration needed
   - Check logs: `docker logs alexandria-backend | grep -E "schema|migration"`
   - You should see: "✓ Database migrations completed successfully"

3. **Existing admin users are preserved**:
   - Auto-admin creation only runs if NO users exist
   - Your existing users and passwords are unchanged

**New Features:**
- ✅ Automatic database initialization on first run
- ✅ Auto-creates admin user from environment variables
- ✅ Multi-user authentication with sessions
- ✅ Role-based access control (Admin/User roles)
- ✅ Automatic schema migrations on upgrade
- ✅ Database backup/restore CLI commands

**Migration Guide:**

```bash
# 1. Stop container
# 2. Edit template, add new variables:
#    - ALEXANDRIA_ADMIN_USERNAME=admin (or leave existing)
#    - ALEXANDRIA_ADMIN_PASSWORD=(will be ignored if users exist)
#    - ALEXANDRIA_SECRET_KEY=(generate new: openssl rand -base64 32)
# 3. Start container
# 4. Check logs for successful migration
# 5. Access web UI and login with existing credentials
```

**For Fresh Installs:**

The setup is now even easier:

1. Install PostgreSQL from Community Apps
2. Install alexandria-backend
3. Set required variables:
   - Database connection (host, user, password)
   - Admin credentials (username, password)
   - Secret key (generate random string)
4. Start container
5. Everything initializes automatically!

No manual database setup needed!

### alexandria.xml - No Changes

The frontend template remains unchanged. It communicates with the backend API which handles all authentication.

---

## Template Version History

### v1.0.0 (2025-12-27)
- Added authentication variables
- Added security configuration
- Updated overview with auto-initialization features
- Added first-run instructions
- Database auto-initialization
- Schema version tracking

### v0.1.0 (2025-12-23)
- Initial Unraid template (pre-release)
- Database configuration
- Library path mapping
- Basic settings
