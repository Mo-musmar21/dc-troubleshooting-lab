# Incident 002: Disk Capacity Alert

## Timestamp
Mon Nov  9 00:23:38 +03 2026

## Alert Trigger
Disk usage at ~85% on /mnt/dc-lab-disk volume.

## Investigation Steps
1. Identified full filesystem with df -h
2. Located largest files with du -sh | sort -rh
3. Checked for growing log files with find
4. Confirmed file ownership with ls -la

## Root Cause
Large temporary file (fill-file, ~80MB) consuming 80% of the 100MB volume.

## Remediation
- Removed fill-file: sudo rm /mnt/dc-lab-disk/fill-file
- Space reclaimed to normal operating levels

## Verification
- df -h /mnt/dc-lab-disk confirms usage below 50%
