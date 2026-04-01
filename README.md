# homepage-config
Personal collection of config files for [gethomepage / homepage](https://github.com/gethomepage/homepage) 

Dark Theme
<img width="1693" height="578" alt="image" src="https://github.com/user-attachments/assets/5d0939e5-9369-4499-a042-295e4c575018" />

Light Theme
<img width="1686" height="567" alt="image" src="https://github.com/user-attachments/assets/56c27417-54af-42dd-baa5-34ba60dfa1d4" />


## WTB:
* mask all User / Pass / API Keys into envs
* post finished layout
* add failover via NGINX
* toggle icons depending on the dark / light theme

# Homepage Configuration

My high-level, privacy-friendly setup for a self-hosted Homepage dashboard.

This repository is organized to keep the dashboard layout clean, the configuration readable, and sensitive values out of version control. It separates page structure, service definitions, and runtime secrets so the final dashboard stays easy to maintain.

## Overview

This setup is built around three main configuration files plus environment variables:

- `settings.yaml` controls the overall page layout
- `widgets.yaml` defines the top information widgets
- `services.yaml` defines the grouped service cards
- environment variables inject URLs, usernames, passwords, and API keys at runtime

Together, these parts are merged by Homepage into a single dashboard.

## Page model

```text
                 HOMEPAGE PAGE MODEL

   widgets.yaml                      settings.yaml
   ├─ search                         ├─ fullWidth: true
   ├─ datetime                       ├─ maxGroupColumns
   ├─ summary widgets                └─ group style: columns
   ├─ greeting
   └─ logo
          \                              /
           \                            /
            \                          /
             └────── Homepage ────────┘
                       │
                       ├─ top info-widget area
                       │    [logo] [greeting] [search] [date/time]
                       │    [summary widgets]
                       │
                       └─ main grouped card area
                            [Storage] [Network] [Utilities] [Media] [Services]


```

## How it works

```text
   settings.yaml
      │
      ├─ controls page width
      ├─ controls max number of group columns
      └─ controls how service groups are laid out

   widgets.yaml
      │
      ├─ controls top/info widgets
      ├─ adds search and date/time
      ├─ adds summary widgets
      └─ adds decorative/navigation widgets

   services.yaml
      │
      ├─ controls the main grouped cards
      ├─ gives each card links, health checks, and widgets
      └─ defines what appears in each section

   env vars
      │
      └─ inject actual runtime secrets and endpoints

   Homepage
      │
      └─ merges all of it into one page

```


## Environment variables


Secrets and endpoints are kept outside the YAML files.

Typical values:

`HOMEPAGE_VAR_URL_*`
`HOMEPAGE_VAR_USER_*`
`HOMEPAGE_VAR_PASS_*`
`HOMEPAGE_VAR_KEY_*`

This keeps the repository portable and safer to share.

## Rendering flow

```text
                 ┌──────────────────────────────┐
                 │         settings.yaml        │
                 │                              │
                 │  - page width                │
                 │  - max group columns         │
                 │  - per-group layout style    │
                 └──────────────┬───────────────┘
                                │
                                │ controls page structure
                                │
                 ┌──────────────▼───────────────┐
                 │         widgets.yaml         │
                 │                              │
                 │  - search                    │
                 │  - datetime                  │
                 │  - greeting / logo           │
                 │  - summary widgets           │
                 └──────────────┬───────────────┘
                                │
                                │ defines top widget area
                                │
                 ┌──────────────▼───────────────┐
                 │        services.yaml         │
                 │                              │
                 │  - grouped service cards     │
                 │  - links                     │
                 │  - health checks             │
                 │  - service widgets           │
                 └──────────────┬───────────────┘
                                │
                                │ defines main dashboard content
                                ▼
                 ┌──────────────────────────────┐
                 │       HOMEPAGE APP/PARSER    │
                 │                              │
                 │  reads all YAML files        │
                 │  resolves placeholders       │
                 │  builds widgets and cards    │
                 │  runs checks and API calls   │
                 └──────────────┬───────────────┘
                                │
                                │ injects runtime values &
                                │ replaces placeholders like
                                │ {{HOMEPAGE_VAR_*}}
                                ▼
                 ┌──────────────────────────────┐
                 │       ENVIRONMENT VARS       │
                 │                              │
                 │  URL_*   USER_*              │
                 │  PASS_*  KEY_*               │
                 └──────────────┬───────────────┘
                                │
        ┌───────────────────────┼────────────────────────┬──────────────────────┐
        │                       │                        │                      │
        ▼                       ▼                        ▼                      ▼

┌──────────────────┐   ┌──────────────────┐   ┌──────────────────┐   ┌─────────────────────┐
│ href             │   │ siteMonitor      │   │ ping             │   │ widget              │
│                  │   │                  │   │                  │   │                     │
│ clickable tile   │   │ HTTP/HTTPS check │   │ ICMP host check  │   │ fetch live API data │
│ destination      │   │ + response time  │   │ host reachable   │   │ stats/status/info   │
└────────┬─────────┘   └────────┬─────────┘   └────────┬─────────┘   └──────────┬──────────┘
         │                      │                      │                          │
         ▼                      ▼                      ▼                          ▼

 open service in browser   green/red status dot    simple reachability       service-specific
 when card is clicked      and latency display     test for host/IP          content appears

```
