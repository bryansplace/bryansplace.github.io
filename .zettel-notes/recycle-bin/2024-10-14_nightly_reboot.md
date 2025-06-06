---
title: Nightly reboot
date:2024-10-14
tags:
---


## Enable and Start crond (if not already running)

rc-service crond start
rc-update add crond

## Edit the Crontab

Run

crontab -e

Then add the following line:

0 1 * * * /sbin/reboot

This schedules a reboot every day at 1:00 AM.

### Save and Exit

If using vi, press Esc, type :wq, and hit Enter.

## Verify the Cron Job

Check the existing cron jobs with:

crontab -l