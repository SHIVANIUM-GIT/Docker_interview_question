# Docker Real-world Scenario Interview Questions

A curated list of Docker command-line use cases that frequently appear in real-world scenarios and technical interviews.

---

## 1. Build Docker Image Without Cache

### Scenario:

You want to ensure that your Docker image is rebuilt completely from scratch without using any cached layers.

### Command:

```bash
docker build --no-cache --tag myapp .
```

### Explanation:

The `--no-cache` flag disables caching for all steps in the Dockerfile, ensuring a clean build. This is especially useful when dependencies or base images might change, and you want to avoid outdated layers.

---

## 2. Dockerfile in a Different Directory

### Scenario:

You have your Dockerfile located in a different folder and want to build the image from there.

### Command:

```bash
docker build --tag myapp -f docker/Dockerfile .
```

### Explanation:

The `-f` flag allows you to specify the location of the Dockerfile. Ensure to specify the build context (`.` in this case) correctly.

---

## 3. Use Git Commit ID as Docker Tag

### Scenario:

You want to tag your Docker image with the current Git commit hash for traceability.

### Command:

```bash
docker build --tag myapp:$(git rev-parse --short HEAD) .
```

### Explanation:

`git rev-parse --short HEAD` fetches the short form of the current Git commit hash, helping to version the Docker image by the code commit.

---

## 4. Use Multi-stage Build to Optimize Image

### Scenario:

Your image size is large because it includes build tools or unnecessary files.

### Dockerfile:

```Dockerfile
# Stage 1 - Build
FROM golang:1.21 as builder
WORKDIR /app
COPY . .
RUN go build -o myapp

# Stage 2 - Runtime
FROM alpine:latest
COPY --from=builder /app/myapp /myapp
ENTRYPOINT ["/myapp"]
```

### Explanation:

Multi-stage builds allow you to use one image for building and another lightweight image for production, reducing the final image size.

---

## 5. Use Date as Docker Tag

### Scenario:

You want to include the current date in the Docker image tag for easier version tracking.

### Command:

```bash
docker build --tag myapp:release-$(date +%Y-%m-%d) .
```

### Explanation:

This tags your image with the current date, useful for nightly or daily builds.

---

## 6. Set RAM and CPU Limits

### Scenario:

You want to restrict the amount of memory and CPU a container can use.

### Command:

```bash
docker run --memory="512m" --cpus="1.5" myapp:latest
```

### Explanation:

This ensures the container uses a maximum of 512MB of RAM and 1.5 CPU cores, which helps in resource-constrained environments.

---

## 7. Run as Non-root User

### Scenario:

You want your container to run with a non-root user for security purposes.

### Command:

```bash
docker run -u 1001:1001 myapp:latest
```

### Explanation:

Running containers as a non-root user is a best practice to prevent privilege escalation attacks.

---

## 8. Run Container as Read-only

### Scenario:

You want to restrict write access in the container for extra security.

### Command:

```bash
docker run --read-only --tmpfs /tmp -d myapp:latest
```

### Explanation:

This mounts the root filesystem as read-only and adds a temporary writable `/tmp` to meet application needs.

---

## 9. Setup Automatic Restart

### Scenario:

You want the container to automatically restart if it fails.

### Command:

```bash
docker run -d --restart=on-failure:3 myapp sh -c "sleep 1 && exit 1"
```

### Explanation:

This will restart the container up to 3 times on failure. Useful for fault-tolerant setups.

---

## 10. Health Check for Container

### Scenario:

You want Docker to monitor the health of a running container.

### Dockerfile:

```Dockerfile
FROM node:18
WORKDIR /app
COPY server.js .
RUN apk add --no-cache curl && npm install express
EXPOSE 8080
HEALTHCHECK CMD curl --fail http://localhost:8080/health || exit 1
CMD ["node", "server.js"]
```

### Explanation:

The `HEALTHCHECK` instruction checks the `/health` endpoint, and Docker marks the container unhealthy if it fails.

---

## 11. Tail Container Logs

### Scenario:

You want to monitor the latest logs of a container in real-time.

### Command:

```bash
docker logs -f --tail 50 <container-id>
```

### Explanation:

This displays the last 50 lines of logs and continues to stream new logs as they are generated.

---

## 12. Check Specific Process in Container

### Scenario:

You want to verify if a certain process (e.g., Java) is running inside a container.

### Command:

```bash
docker exec -it <container-id> sh -c "ps aux | grep java"
```

### Explanation:

This inspects the running processes inside the container using `ps`.

---

## 13. Run Container in Restricted Mode

### Scenario:

You want to harden your container environment.

### Command:

```bash
docker run --pids-limit 100 --read-only --memory=256m myapp
```

### Explanation:

Limits the number of processes, enforces read-only FS, and sets memory usage limits.

---

## 14. Get Logs on Host Machine

### Scenario:

You want container logs written to a directory on your host.

### Command:

```bash
docker run -v $(pwd)/logs:/var/log/nginx nginx
```

### Explanation:

This mounts a host directory into the container where logs can be written persistently.

---

## 15. Clean Up Unused Docker Resources

### Command:

```bash
docker system prune -af --volumes
```

### Explanation:

Removes all unused containers, images, networks, and volumes to free up disk space.

---

## 16. Delete Dangling Images

### Command:

```bash
docker rmi $(docker images -f "dangling=true" -q)
```

### Explanation:

Deletes untagged images that are not used by any container.

---

## 17. Delete All Containers

### Command:

```bash
docker rm -f $(docker ps -aq)
```

### Explanation:

Forcibly deletes all running and stopped containers.

---

## 18. List Images Created After Specific Image

### Command:

```bash
docker images --filter "since=<image-name:tag>"
```

### Explanation:

Shows only the images that were created after a given image.

---

## 19. Rename a Container

### Command:

```bash
docker rename <old-container-name> <new-container-name>
```

### Explanation:

Changes the container's name without restarting it.

---

## 20. Set Restart Policy

### Command:

```bash
docker update --restart=always <container-name>
```

### Explanation:

Configures the container to always restart unless explicitly stopped.

---

## 21. Pause and Unpause Container

### Command:

```bash
docker pause <container-name>
docker unpause <container-name>
```

### Explanation:

Temporarily suspends/resumes all processes within a container.

---

## 22. Check Files Modified in Container

### Command:

```bash
docker diff <container-name>
```

### Explanation:

Lists changes to files or directories in a container’s filesystem.

---

## 23. Export Docker Image

### Command:

```bash
docker save <image-name> > <image-name>.tar
```

### Explanation:

Saves the Docker image to a tar archive for transfer or backup.

---

## 24. Import Docker Image

### Command:

```bash
docker load < <image-name>.tar
```

### Explanation:

Loads a Docker image from a tar file into the local Docker registry.

---

## 25. Copy File from Container to Host

### Command:

```bash
docker cp <container-name>:<file-path> ./localfile.txt
```

### Explanation:

Extracts a file from a running container to the host.

---

## 26. Check Real-time CPU and Memory Usage

### Command:

```bash
docker stats --no-stream
```

### Explanation:

Provides a live snapshot of resource usage by running containers.

---
