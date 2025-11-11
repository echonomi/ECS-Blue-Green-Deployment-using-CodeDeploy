# ---- Base Node image ----
FROM node:18-alpine AS base

# Set working directory
WORKDIR /app

# ---- Install dependencies ----
FROM base AS deps
COPY package*.json ./
RUN npm install --production=false

# ---- Build the application ----
FROM deps AS build
COPY . .
RUN npm run build

# ---- Production image ----
FROM node:18-alpine AS prod
WORKDIR /app

COPY package*.json ./
RUN npm install --production

# Copy only build output + node_modules
COPY --from=build /app/dist ./dist

EXPOSE 3000
CMD ["node", "dist/main.js"]