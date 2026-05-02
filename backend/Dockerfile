# syntax=docker/dockerfile:1

FROM node:22-alpine AS build

WORKDIR /repo

# Install deps from monorepo root (npm workspaces)
COPY package.json package-lock.json ./
COPY backend/package.json ./backend/package.json

RUN npm ci --legacy-peer-deps

# Build backend
COPY backend/tsconfig.json ./backend/tsconfig.json
COPY backend/src ./backend/src

RUN npm run build --workspace backend


FROM node:22-alpine AS runtime

ENV NODE_ENV=production
WORKDIR /app

# Install production deps (workspace-aware install from root)
COPY package.json package-lock.json ./
COPY backend/package.json ./backend/package.json

RUN npm ci --legacy-peer-deps --omit=dev

# Copy compiled backend
COPY --from=build /repo/backend/dist ./backend/dist

EXPOSE 5000

CMD ["node", "backend/dist/server.js"]
