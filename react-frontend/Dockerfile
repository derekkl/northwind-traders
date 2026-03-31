FROM registry.access.redhat.com/ubi9/nodejs-18:latest as build
WORKDIR /opt/app-root/src
ENV PATH /opt/app-root/src/node_modules/.bin:$PATH
# Ensure we copy the package files first for better caching
COPY --chown=1001 package*.json ./
COPY --chown=1001 maven/ ./
RUN npm install && npm run build

FROM registry.access.redhat.com/ubi9/nodejs-18:latest as run
WORKDIR /opt/app-root/src

# Copy the build artifacts and the server code
COPY --chown=1001 --from=build /opt/app-root/src/build ./build
COPY --chown=1001 --from=build /opt/app-root/src/index.js ./index.js
# Copy package files to the run stage so we can install production dependencies
COPY --chown=1001 --from=build /opt/app-root/src/package*.json ./

# Install ONLY production dependencies (Express 4 and the proxy)
# This uses your fixed package.json versions!
RUN npm install --only=production

EXPOSE 8080
CMD ["node", "index.js"]
