## YouTube Link: 

https://www.youtube.com/watch?v=wNK-sRuGl5c



## 1. What are the main differences between a Docker image and a Docker container?

A docker image is a packaged template for an application. 

A docker container is an instance created from that image. 

| Aspect          | Docker image                                                 | Docker container                                             |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Contents        | Application code, dependencies, filesystem layers, and default settings | Image contents plus its own writable layer and runtime settings |
| Execution       | Cannot run by itself                                         | Can be running or stopped                                    |
| Changes         | Image layers are read-only; you build a new image to update them | Files can change in the container’s writable layer           |
| Relationship    | One image can create many containers                         | Each container is created from an image                      |
| Listing command | `docker images`                                              | `docker ps -a`                                               |

## 2. Explain how Docker's layered architecture improves efficiency.

Docker images are built from **read-only layers**, with each layer recording filesystem changes. For example, your Python image contains base OS files, Python, installed dependencies, and your application code.

This improves efficiency in four main ways:

- **Less disk usage:** Images and containers can share identical layers. Your `app1` and `app2` reuse the same image layers instead of storing two complete copies.
- **Faster builds:** Docker reuses cached build steps when their inputs haven’t changed. In your Dockerfile, changing only `app.py` lets Docker reuse the earlier dependency installation step.
- **Smaller transfers:** When pulling or pushing images, Docker transfers only layers missing from the destination. An application update can reuse existing base layers.
- **Efficient container creation:** Each container adds a thin **writable layer** over the image. With copy-on-write, modifying an image file creates a private copy in that container’s writable layer, leaving the shared image unchanged.

So two containers can **share the same application files while keeping their own changes separate**.

## 3. Why does each container get its own writable layer?

Each container gets its own writable layer so it can **change files independently while sharing the same read-only image**.

When reading a file, Docker presents the container’s writable layer together with the image layers. **A container’s modified copy takes precedence over the original image file.**

This gives containers independent filesystems without copying the entire image for each container. Paths backed by shared volumes or bind mounts can intentionally share data.

## 4. What are the benefits of using Docker Compose over running containers individually?

Docker Compose lets you **define and manage a multi-container application in one YAML file**, instead of issuing separate `docker run` commands.

| Benefit                              | How it helps                                                 |
| ------------------------------------ | ------------------------------------------------------------ |
| **Simpler management**               | Start your web and Redis services together with `docker compose up -d`. |
| **Repeatable configuration**         | Keep image names, ports, environment variables, networks, and volumes in a file rather than remembering command options. |
| **Automatic networking**             | Services can reach each other by service name—for example, your web app connects to `redis:6379`. |
| **Dependency management**            | Use `depends_on` to control startup order. Add health checks and `service_healthy` when a dependency must be ready before the app starts. |
| **Persistent storage configuration** | Declare volumes such as your `redis-data` volume alongside the services that use them. |
| **Easy sharing**                     | Share the Compose file and application files so others can reproduce the setup. |
| **Centralized operations**           | View all service logs with `docker compose logs`, or stop all services with `docker compose stop`. |