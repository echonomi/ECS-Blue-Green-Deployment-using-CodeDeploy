— In this guide, we are going to containerize our application. To get started with Docker, ensure that `Docker` is installed on your machine and that you have a `Dockerfile` located at the root level of your repository.

>[!Note]
>The structure of your `Dockerfile` will depend on your application's runtime environment. You can visit [[https://hub.docker.com/|Docker Hub]] to find base images or reference templates suitable for you application.

If you followed `STEP 1` and created the same `Nest.js` application you can used the provided `Dockerfile` structure below:

```Dockerfile
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

```

Make sure the `Dockerfile` is placed in the root level of your repository

![[Screenshot 2025-11-11 at 10.48.19.png]]
### Create Docker Image
After you setup the `Dockerfile`, use the command below to create you `Docker` image

```
docker build -t <image-name> .
```

>[!Important Reminder]
>If you are using macOS with an Apple Silicon (M1/M2/M3) processor, build your Docker image with the following command:
>`docker buildx build --platform linux/amd64,linux/arm64 -t [image-name] .`
>This ensures your image is built for both architectures, preventing compatibility issues when running the container later on AWS.

![[Pasted image 20251111110730.png]]

You can check your docker images by running the command below:

```shell
docker images
```

![[Pasted image 20251111110835.png]]

### Run Docker Image Locally
To verify that the Docker image works correctly, you can run it locally using the command below:

```shell
docker run -p 3000:3000 <image-name>
```

![[Pasted image 20251111111230.png]]

This will start your application inside a container and expose it on port 3000.
Open your browser and navigate to http://localhost:3000 to confirm everything is working as expected.

You should observe the same behavior from your application as before containerization.

![[Pasted image 20251111111420.png]]

Thats it you have containerized your application 🎉