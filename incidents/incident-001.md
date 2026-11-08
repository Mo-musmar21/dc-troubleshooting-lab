# Incident 001: nginx Service Failure

## Symptom
nginx not responding. `curl -I http://localhost` returns "Connection refused."

## Root Cause
Configuration syntax error: missing semicolon on `listen 80` directive in `/etc/nginx/sites-available/default`. Invalid line "BROKEN" appended to `/etc/nginx/nginx.conf`.

## Fix Applied
1. Removed invalid "BROKEN" line from `/etc/nginx/nginx.conf`
2. Validated configuration with `sudo nginx -t` (syntax OK)
3. Started service with `sudo systemctl start nginx`

## Verification
- `systemctl status nginx` shows active (running)
- `curl -I http://localhost` returns HTTP/1.1 200 OK
