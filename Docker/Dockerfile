# This Dockerfile is to build an image/microservice to host a static web-page

# Which base image to use? 
FROM nginx

# Who is building it?
LABEL MAINTAINER=SHAFIQUE_M@HOTMAIL.CO.UK

# Copy the index.html from localhost to /usr/share/nginx/html/
COPY index.html /usr/share/nginx/html/

# Which ports to expose/enable
EXPOSE 80

# Launch the server / nginx official command using CMD
CMD ["nginx", "-g", "daemon off;"]

# Ensure to create this Dockerfile where the index.html is located
