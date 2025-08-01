# Use a Node.js image as the base for building the application
FROM node:22-alpine AS builder

# Set the working directory inside the container
WORKDIR /app

# Copy package.json and package-lock.json to install dependencies
COPY mcp_servers/dropbox/package.json mcp_servers/dropbox/package-lock.json ./

# Install dependencies (ignoring scripts to prevent running the prepare script)
RUN npm install --ignore-scripts

# Copy the rest of the application source code
COPY mcp_servers/dropbox .

# Build the application using TypeScript
RUN npm run build

# Use a smaller Node.js image for the final image
FROM node:22-slim AS release

# Set the working directory inside the container
WORKDIR /app

# Copy the built application from the builder stage
COPY --from=builder /app/build /app/build
COPY --from=builder /app/package.json /app/package.json
COPY --from=builder /app/package-lock.json /app/package-lock.json
# Install only production dependencies
RUN npm ci --omit=dev --ignore-scripts

EXPOSE 5000

# Specify the command to run the application
ENTRYPOINT ["node", "build/src/index.js"]
