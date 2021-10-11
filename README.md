alertmanager-discord
===

Give this a webhook (with the DISCORD_WEBHOOK environment variable) and point it as a webhook on alertmanager, and it will post your alerts into a discord channel for you as they trigger:

![](/.github/demo-new.png)

## Warning

This program is not a replacement to alertmanager, it accepts webhooks from alertmanager, not prometheus.

The standard "dataflow" should be:

```
Prometheus -------------> alertmanager -------------------> alertmanager-discord

alerting:                 receivers:                         
  alertmanagers:          - name: 'discord_webhook'         environment:
  - static_configs:         webhook_configs:                   - DISCORD_WEBHOOK=https://discordapp.com/api/we...
    - targets:              - url: 'http://localhost:9094'  
       - 127.0.0.1:9093   





```
## Tag Users and Roles
In discord find Your role/user UID with `\@<roleName|username>`. For user copy 18 digits between `<@>` and for roles copy &+digits between `<@>` and add them under `annotations` part of Your alert. If IDs does not match with `^(&)?\d{18}$` regex it will ignore that ID. example:  
```
---
groups:
- name: Hello
  rules:
  - alert: InstanceIsDown
    expr: up{} == 0
    for: 5m
    annotations:
      summary: "Instance is down"
      description: "{{ $labels.instance }} is down."
      # add your ids here comma-separated
      mustTags: "&896806229014333333,513283200966063333"
...
```
## Example alertmanager config:

```
global:
  # The smarthost and SMTP sender used for mail notifications.
  smtp_smarthost: 'localhost:25'
  smtp_from: 'alertmanager@example.org'
  smtp_auth_username: 'alertmanager'
  smtp_auth_password: 'password'

# The directory from which notification templates are read.
templates: 
- '/etc/alertmanager/template/*.tmpl'

# The root route on which each incoming alert enters.
route:
  group_by: ['alertname']
  group_wait: 20s
  group_interval: 5m
  repeat_interval: 3h 
  receiver: discord_webhook

receivers:
- name: 'discord_webhook'
  webhook_configs:
  - url: 'http://localhost:9094'
```
