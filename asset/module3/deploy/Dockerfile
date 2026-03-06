# Minimal image for Lab 5B: Docker image deployment
# Serves static content via nginx (no git on server; deploy by pull + run)
FROM nginx:1.27-alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
