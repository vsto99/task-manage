# syntax=docker/dockerfile:1

FROM node:22-alpine AS build

WORKDIR /repo

# Install deps from monorepo root (npm workspaces)
COPY package.json package-lock.json ./
COPY frontend/package.json ./frontend/package.json

RUN npm ci --legacy-peer-deps

# Build-time API URL for Vite
ARG VITE_API_URL=http://localhost:5000/api
ENV VITE_API_URL=${VITE_API_URL}

COPY frontend/tsconfig.json ./frontend/tsconfig.json
COPY frontend/tsconfig.node.json ./frontend/tsconfig.node.json
COPY frontend/vite.config.ts ./frontend/vite.config.ts
COPY frontend/index.html ./frontend/index.html
COPY frontend/src ./frontend/src

RUN npm run build --workspace frontend


FROM nginx:1.27-alpine

COPY frontend/nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=build /repo/frontend/dist /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
