---
title: WikiJS搭建
description: 快速搭建说明
published: true
date: 2026-01-25T09:38:22.037Z
tags: 
editor: markdown
dateCreated: 2026-01-25T09:38:22.037Z
---

# WikiJS搭建说明
`docker-compose.yml`
```js
version: "3.9"

networks:
  wikinet:
    driver: bridge

volumes:
  pgdata:

services:
  db:
    image: postgres:17
    container_name: db
    hostname: db
    restart: unless-stopped
    networks:
      - wikinet
    environment:
      POSTGRES_DB: wiki
      POSTGRES_USER: wiki
      POSTGRES_PASSWORD_FILE: /etc/wiki/.db-secret
    volumes:
      - /etc/wiki/.db-secret:/etc/wiki/.db-secret:ro
      - pgdata:/var/lib/postgresql/data

  wiki:
    image: ghcr.io/requarks/wiki:2
    container_name: wiki
    hostname: wiki
    restart: unless-stopped
    networks:
      - wikinet
    depends_on:
      - db
    ports:
      - "80:3000"
      - "443:3443"
    environment:
      DB_TYPE: postgres
      DB_HOST: db
      DB_PORT: 5432
      DB_USER: wiki
      DB_NAME: wiki
      DB_PASS_FILE: /etc/wiki/.db-secret
      UPGRADE_COMPANION: "1"
    volumes:
      - /etc/wiki/.db-secret:/etc/wiki/.db-secret:ro

  wiki-update-companion:
    image: ghcr.io/requarks/wiki-update-companion:latest
    container_name: wiki-update-companion
    hostname: wiki-update-companion
    restart: unless-stopped
    networks:
      - wikinet
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
```
