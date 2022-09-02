# Dockerfile

```
# Builder
FROM node:12-alpine as dist
WORKDIR /build
COPY package.json package-lock.json /build/
RUN npm ci
COPY . /build/
RUN npm run build

# Runner
FROM node:12-alpine
WORKDIR /app
ENV NODE_ENV production
RUN chown -R node:node /app
USER node
COPY --from=dist /build/package.json /build/package-lock.json /app/
RUN npm --only=production ci
COPY --from=dist /build/dist /app/dist/
```
