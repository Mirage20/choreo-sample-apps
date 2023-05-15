# FROM node:18-alpine as builder

# # Build arguments for user/group configurations
# ARG USER_ID=10001
# ARG USER_GROUP_ID=10001
# ARG USER_HOME=/home/app

# # Create app directory
# WORKDIR ${USER_HOME}

# # Copy the rest of the application code to the container
# COPY --chown=${USER_ID}:${USER_GROUP_ID} . .

# # Set environment variables
# ENV HOST="0.0.0.0"
# ENV DISABLE_DEV_SERVER_HOST_CHECK=true
# ENV HTTPS=false

# # Install dependencies
# # RUN npm install --save --legacy-peer-deps 
# RUN npm install --save --legacy-peer-deps
# RUN mkdir -p node_modules/.cache && chmod -R 777 node_modules/.cache

# USER 10001

# # Set the env to "production"
# ENV NODE_ENV production

# EXPOSE 3000

# # Start the application
# CMD ["npm", "start", "--cache", "/tmp"]


FROM node:18.16.0-alpine AS builder
WORKDIR /app
COPY . .
# Install dependencies (npm ci makes sure the exact versions in the lockfile gets installed)
RUN npm ci 
RUN npm run build

FROM nginxinc/nginx-unprivileged:1.24-alpine as production
WORKDIR /app
ENV NODE_ENV production
# Copy built assets from the 1st stage
COPY --from=builder /app/build /usr/share/nginx/html
# Add your routing config to nginx
COPY nginx-conf/app-default.conf /etc/nginx/conf.d/app-default.conf
EXPOSE 3000
# add user
USER 10014
# Start nginx
CMD ["nginx", "-g", "daemon off;"]
