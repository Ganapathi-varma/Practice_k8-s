# syntax=docker/dockerfile:1

# Base image: lightweight nginx on Alpine
FROM nginx:1.27-alpine

# OCI metadata (good practice for private registries)
LABEL org.opencontainers.image.title="k8s-slides" \
      org.opencontainers.image.description="Static HTML slides: Kubernetes ConfigMaps, Secrets, Namespaces, DaemonSets" \
      org.opencontainers.image.source="https://your.private.repo/k8s-slides"

# Remove the default nginx welcome page
RUN rm -rf /usr/share/nginx/html/*

# Copy all the HTML slide pages into the web root
COPY *.html /usr/share/nginx/html/

# Serve on port 80
EXPOSE 80

# Basic container healthcheck
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD wget -q --spider http://localhost/configmaps-and-secrets.html || exit 1

# nginx runs in the foreground by default via the base image CMD
