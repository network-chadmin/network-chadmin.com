---
title: Containerlab
icon:
order: 1
---

# Containerlab Reference Guide

## Deployment Commands

| Command | Description |
|---------|-------------|
| `containerlab deploy` | Deploy lab (must in directory with lab file.) |
| `containerlab deploy -t <lab-name.clab.yml` | Deploy specific lab file. |
| `containerlab deploy --reconfigure` | Deploy lab with fresh set of files (config/TLS/etc.) |
| `clab dep` | Deploy a lab using alias's |

## Management Commands

| Command | Description |
|---------|-------------|
| `containerlab inspect` | Show running lab in local directory |
| `containerlab inspect -it <labfile.clab.yml>` | Show specific lab |
| `containerlab inspect -a` | Show all labs |
| `containerlab destroy -t <labfile.clab.yml>` | Destroy specific lab |
| `containerlab destroy -t <labfile.clab.yml> --cleanup` | Destroy lab and delete extra lab file |
| `containerlab save -t <labfile.clab.yml>` | Save node configurations from specific lab |

## Docker Commands for Management

| Command | Description |
|---------|-------------|
| `docker ps` | List running containers |
| `docker restart <container>` | Restart container |
| `docker stop <container>` | Stop container |
| `docker rm <container>` | Remove container |

## Container Access

| Command | Description |
|---------|-------------|
| `docker exec -it <container> bash` | Access container bash shell |
| `docker exec -it <container> Cli` | Access container CLI (Arista) |

## Image Management

| Command | Description |
|---------|-------------|
| `docker images` | List available images |
| `docker rmi <image>` | Remove an image |
| `docker pull <image>` | Attempt to download image from Docker |
| `docker import <file> <name>:<tag>` | Import image from file |