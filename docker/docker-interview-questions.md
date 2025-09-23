# Docker Interview Questions and Answers (Basic to Advanced)

## Basic Questions

**1. What is Docker?**
Docker is an open-source platform that automates the deployment, scaling, and management of applications using containerization. Containers package an application and its dependencies together, ensuring consistency across environments.

**2. What is the difference between a Docker image and a container?**
- Image: A read-only template with instructions for creating a container.
- Container: A running instance of an image, isolated from the host and other containers.

**3. How do you run a container from an image?**
```bash
docker run -d --name mynginx -p 8080:80 nginx:alpine
```

**4. How do you list running containers?**
```bash
docker ps
```

**5. How do you stop and remove a container?**
```bash
docker stop mynginx
docker rm mynginx
```

## Intermediate Questions

**6. How do you write a Dockerfile for a Node.js app?**
```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "index.js"]
```

**7. What is a Docker volume? How do you use it?**
A volume is a persistent storage mechanism for containers.
```bash
docker run -v mydata:/data myimage
```

**8. How do you share data between containers?**
- Use named volumes or shared host directories.
- Example:
```bash
docker run -v sharedvol:/shared --name c1 busybox
docker run -v sharedvol:/shared --name c2 busybox
```

**9. How do you inspect a running container?**
```bash
docker exec -it <container> /bin/sh
docker logs <container>
docker inspect <container>
```

**10. How do you optimize Docker images?**
- Use multi-stage builds, `.dockerignore`, minimal base images, and combine RUN commands.

## Advanced Questions

**11. What is a multi-stage build? Give an example.**
```dockerfile
FROM golang:1.21 AS builder
WORKDIR /src
COPY . .
RUN go build -o app

FROM alpine
COPY --from=builder /src/app /app
ENTRYPOINT ["/app"]
```

**12. How do you handle secrets in Docker?**
- Use Docker secrets (Swarm), environment variables, or external secret managers (e.g., HashiCorp Vault).

**13. How do you update a running service in Docker Swarm?**
```bash
docker service update --image myimage:latest myservice
```

**14. How do you network containers?**
```bash
docker network create mynet
docker run -d --network mynet --name app1 myimage1
docker run -d --network mynet --name app2 myimage2
```

**15. How do you scan Docker images for vulnerabilities?**
```bash
trivy image myimage:tag
```

**16. How do you reduce the attack surface of a container?**
- Use minimal images, run as non-root, remove unnecessary packages.

**17. How do you build and push an image to a private registry?**
```bash
docker build -t myregistry.com/myimage:tag .
docker login myregistry.com
docker push myregistry.com/myimage:tag
```

**18. How do you troubleshoot a failing container?**
- Check logs, inspect, run with interactive shell, check resource limits.

**19. What is the difference between ENTRYPOINT and CMD in Dockerfile?**
- ENTRYPOINT sets the main command, CMD provides default arguments.

**20. How do you limit resources for a container?**
```bash
docker run --memory=512m --cpus=1 myimage
```

**21. How do you clean up unused images, containers, and volumes?**
```bash
docker system prune -a
```

**22. How do you use health checks in Docker?**
```dockerfile
HEALTHCHECK CMD curl --fail http://localhost:3000/health || exit 1
```

**23. How do you orchestrate containers in production?**
- Use Docker Compose for development, Docker Swarm or Kubernetes for production.

---
This list covers Docker interview questions and answers from basic to advanced, with code examples.
