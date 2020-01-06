# Use a node 12 base image
ARG NODE_VERSION=12
FROM node:${NODE_VERSION}-alpine

# Create the app directory
WORKDIR /usr/src/app

# Copy context files
COPY . .

# Install dependencies
RUN npm install

# Build the app
RUN npm run build

# Start the app
ENTRYPOINT ["npm", "run", "start:prod"]

