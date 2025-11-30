# EventHub Deployment Guide

Complete guide to deploy EventHub on InfinityFree (PHP + MySQL only - No external services required).

## Table of Contents
1. [InfinityFree Deployment (PHP App)](#infinityfree-deployment-php-app)
2. [Database Setup](#database-setup)
3. [Email Configuration](#email-configuration)
4. [Post-Deployment Checklist](#post-deployment-checklist)
5. [Troubleshooting](#troubleshooting)

---

## InfinityFree Deployment (PHP App)

### Prerequisites
- InfinityFree account (free tier)
- FTP client (FileZilla, WinSCP, or CyberDuck)
- Your domain or use InfinityFree subdomain

### Step 1: Create InfinityFree Account
1. Go to https://www.infinityfree.net/
2. Click "Sign Up" and create an account
3. Verify your email
4. Create a new hosting account

### Step 2: Get FTP Details
1. Log in to InfinityFree control panel
2. Go to "Manage Account" → "FTP Accounts"
3. Note your FTP username, password, and server address
4. Default root directory is `htdocs`

### Step 3: Prepare PHP App
1. Remove unnecessary files from `php-app/`:
   ```bash
   rm -rf php-app/.git
   rm -rf php-app/node_modules
   rm -rf php-app/.gitignore
   rm php-app/replit.md
   ```

2. Create `.htaccess` in `php-app/` for routing:
   ```apache
   <IfModule mod_rewrite.c>
       RewriteEngine On
       RewriteBase /
       
       # Skip actual files and directories
       RewriteCond %{REQUEST_FILENAME} !-f
       RewriteCond %{REQUEST_FILENAME} !-d
       
       # Route to router.php
       RewriteRule ^(.*)$ router.php?/$1 [L,QSA]
   </IfModule>
   ```

3. Create `php-app/uploads/` directory (for future use)

### Step 4: Upload via FTP
1. Open FileZilla or your FTP client
2. Connect using InfinityFree FTP credentials
3. Navigate to `htdocs` folder
4. Upload all files from `php-app/` to `htdocs/`
   - Keep the folder structure intact
   - Upload `config/`, `includes/`, `pages/`, `api/`, `assets/` directories

### Step 5: Database Setup on InfinityFree
1. Go to control panel → "MySQL Databases"
2. Create a new database (note: InfinityFree adds prefix to DB name)
3. Create a new MySQL user with strong password
4. Add user to database with all privileges

### Step 6: Create Database Schema
1. Go to PhpMyAdmin (control panel → "PhpMyAdmin")
2. Select your database
3. Go to "Import" tab
4. Upload `php-app/config/schema.sql`
5. Click "Go" to execute

### Step 7: Update Configuration Files
1. Edit `config/database.php` via FTP (download, edit, upload):
   ```php
   $host = 'localhost'; // or InfinityFree MySQL host
   $port = 3306;
   $dbname = 'your_database_name';
   $username = 'your_mysql_user';
   $password = 'your_mysql_password';
   ```

2. Edit `config/config.php`:
   ```php
   define('SITE_URL', 'https://yourdomain.com');
   ```

### Step 8: Set Permissions
- Files: 644
- Directories: 755
(Most FTP clients handle this automatically)

### Step 9: Test Deployment
1. Visit `https://yourdomain.infinityfree.com`
2. You should see the EventHub landing page
3. Test registration and login
4. Test creating an event and sending invitations

---

## Database Setup

### InfinityFree MySQL
- Use InfinityFree's free MySQL (1 database included)
- Access via PhpMyAdmin from control panel
- No external database service needed

### Database Schema
The schema includes these tables:
- `users` - User accounts (hosts)
- `events` - Event details
- `guests` - Guest list per event
- `vendors` - Vendor tracking
- `tasks` - Event planning tasks
- `announcements` - Event announcements
- `email_logs` - Email sending history
- `notifications` - User notifications
- `wishes` - Guest guestbook
- `guest_questions` - Q&A system
- `gallery_photos` - Event photos
- `event_functions` - Event functions/stages
- `guest_functions` - Guest invitations to functions
- `host_smtp_settings` - Custom email settings per host
- `menu_items` - Menu management

---

## Email Configuration

### Default System Email (Automatic)
By default, all invitations are sent from: **invitationbyevent@outlook.com**

**Features:**
- No setup required
- Works automatically for all hosts
- Sends beautiful HTML invitations
- Tracks email delivery status

### Custom Host Email (Optional)
Hosts can configure their own email:

1. Host logs into Dashboard
2. Goes to "Email Settings"
3. Enables "Use Custom Email"
4. Enters their SMTP credentials:
   - Email address
   - SMTP server (e.g., smtp-mail.outlook.com)
   - SMTP port (587 for TLS)
   - Password/App password

### Gmail Configuration (for hosts)
1. Enable 2-Factor Authentication on Gmail
2. Go to: https://myaccount.google.com/apppasswords
3. Generate "App Password" for Mail
4. Use this 16-character password in Email Settings

### Outlook Configuration (for hosts)
- SMTP Server: smtp-mail.outlook.com
- Port: 587 (TLS)
- Use main Outlook password or app-specific password

---

## Email Sending System (PHP-Only)

The email system is built entirely in PHP with no external dependencies:

### How It Works
1. Host selects guests to invite
2. Click "Send Invitations"
3. PHP email module sends HTML emails directly via SMTP
4. Each email includes:
   - Personalized invitation message
   - Link to event page
   - RSVP button
   - Calendar download option
   - Venue information

### Email Templates
- Beautiful HTML design with EventHub branding
- Responsive layout for all devices
- Links to public event pages
- QR code generation for entry passes

### Email Tracking
- All emails logged with status (success/failed)
- Resend option for failed emails
- Dashboard shows email statistics

---

## Post-Deployment Checklist

### Before Going Live
- [ ] Database schema is fully imported
- [ ] Admin account is created and tested
- [ ] Email settings are configured
- [ ] SSL certificate is active (HTTPS working)
- [ ] File upload directories have correct permissions
- [ ] `.htaccess` routing is working
- [ ] All pages are accessible and responsive

### Security
- [ ] Change default secrets if needed
- [ ] Database passwords are strong
- [ ] FTP access is restricted (change password)
- [ ] Disable directory listing (via `.htaccess`)
- [ ] Keep PHP updated to latest version
- [ ] Set up regular backups

### Performance
- [ ] Enable gzip compression on InfinityFree
- [ ] Optimize images before upload
- [ ] Cache settings are configured
- [ ] Database indexes are created
- [ ] Limit email sending rate to avoid blacklisting

### Monitoring
- [ ] Set up error logging
- [ ] Monitor database size
- [ ] Track email delivery status
- [ ] Set up uptime monitoring (Uptime Robot)

---

## Troubleshooting

### 404 Errors on InfinityFree
**Problem**: URLs not routing correctly
**Solution**: 
- Ensure `.htaccess` is in `htdocs` folder
- Check that `mod_rewrite` is enabled
- Try disabling URL rewriting if needed

### Email Not Sending
**Problem**: Invitations not being sent
**Solution**:
- Verify SMTP credentials in Email Settings
- Check email logs in dashboard for errors
- Verify firewall isn't blocking SMTP port 587
- Test with PHP's mail() function as fallback

### Database Connection Error
**Problem**: "Can't connect to database"
**Solution**:
- Verify database credentials in `config/database.php`
- Check database is created in PhpMyAdmin
- Verify MySQL user has permissions
- Ensure your IP isn't being blocked

### Session Issues
**Problem**: Sessions not persisting between pages
**Solution**:
- Check session directory permissions (755)
- Verify `session.save_path` in php.ini
- Try using database-backed sessions

---

## Domain Setup

### Using InfinityFree Subdomain
- Default: `yourusername.epizy.com`
- No additional setup needed

### Using Custom Domain
1. Purchase domain from registrar (GoDaddy, Namecheap, etc.)
2. Update nameservers to InfinityFree's:
   - `ns1.epizy.com`
   - `ns2.epizy.com`
   - `ns3.epizy.com`
3. Add domain in InfinityFree control panel
4. Wait 24-48 hours for DNS propagation

---

## Stack Information

### Technology Stack
- **Frontend**: HTML5, CSS3, JavaScript (vanilla, no frameworks)
- **Backend**: PHP 8.2+
- **Database**: MySQL 5.7+
- **Email**: PHP SMTP (no external services)

### File Structure
```
php-app/
├── config/           # Configuration files
├── includes/         # Helper functions & modules
├── pages/           # Admin dashboard pages
├── api/             # API endpoints
├── assets/          # CSS, JS, images
├── e.php            # Public event pages
├── checkin.php      # QR code check-in
└── index.php        # Landing page
```

### Features
- User authentication
- Event management (create, edit, delete)
- Guest management with QR check-in
- Vendor & budget tracking
- Task management
- Announcement system
- Analytics & charts
- Email invitations
- Public event pages
- Guestbook/wishes
- Q&A system
- Gallery management

---

## Quick Start Commands

```bash
# Test PHP locally
php -S 0.0.0.0:5000

# Deploy to InfinityFree (after setup)
# Upload all files from php-app/ to htdocs via FTP

# Create database backup (via PhpMyAdmin)
# Export database as SQL file
# Save locally as backup-YYYY-MM-DD.sql
```

---

## Support

For issues:
1. Check EventHub logs in admin panel
2. Review email logs for delivery status
3. Check PHP error logs
4. Review database for data integrity
5. Contact InfinityFree support for hosting issues

---

**Last Updated**: November 2024  
**Version**: 2.0  
**Status**: Production Ready - PHP + MySQL Only  
**No External Dependencies**: Everything runs on standard PHP hosting!
