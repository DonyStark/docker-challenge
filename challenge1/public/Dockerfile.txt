# Use the official Nginx image from the Docker Hub
FROM nginx:alpine

# Copy the static content to the Nginx html directory
COPY public /usr/share/nginx/html