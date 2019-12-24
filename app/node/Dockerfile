# Use a node 12 base image
ARG NODE_VERSION=12
FROM node:${NODE_VERSION}-alpine

# Create the app directory
WORKDIR /usr/src/app

# Build the app
COPY . .
RUN npm run build

# Start the app
ENTRYPOINT ["npm", "run", "start:prod"]

