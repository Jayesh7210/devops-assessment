# frontend/Dockerfile
# Stage 1: Build
FROM node:20-alpine AS builder

WORKDIR /app

# Copy package files first for better caching
COPY package.json package-lock.json ./
RUN npm ci

# Copy all source files
COPY . .

# Build the React app
RUN npm run build

# Stage 2: Production
FROM nginx:alpine

# Remove default nginx config
RUN rm /etc/nginx/conf.d/default.conf

# Copy custom nginx configuration
COPY nginx.conf /etc/nginx/conf.d/

# Copy built React app from builder stage
COPY --from=builder /app/dist /usr/share/nginx/html

# Set proper permissions
RUN chown -R nginx:nginx /usr/share/nginx/html && \
    chmod -R 755 /usr/share/nginx/html

# Use nginx user (already exists in nginx:alpine image)
USER nginx

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]