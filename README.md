# Go Web Application — Dockerization Guide

This is a simple Go web application that has been dockerized using a multi-stage build process to create a clean, production-ready container image.

---

## 📁 Project Structure

```bash
go-web-app/
├── static/               # Static files (CSS, JS, images)
├── go.mod                # Go modules definition
├── go.sum                # Go modules checksum
├── main.go               # Main application entry point
├── Dockerfile            # Instructions to build the Docker image
└── README.md             # Full documentation (this file)
```

## 📜 Dockerfile Used

This project uses the following Dockerfile:

```bash
# Stage 1: Build
FROM golang:1.22.5 AS base

WORKDIR /app

COPY go.mod .
RUN go mod download

COPY . .
RUN go build -o main .

# Stage 2: Minimal production image
FROM gcr.io/distroless/base

WORKDIR /

COPY --from=base /app/main .
COPY --from=base /app/static ./static

EXPOSE 8080

CMD ["./main"]
```

- The first stage builds the Go application.

- The second stage copies the compiled binary into a minimal image for production.

## 🛠 How to Build and Run Everything

### Build the Docker Image

```bash
 docker build -t cavinton/go-web-app:v1 .
```

- Downloads the Go modules
- Compiles the Go code
- Packages everything into a lightweight container

### Run the Docker Container

```bash
 docker run -p 8080:8080 cavinton/go-web-app:v1
```

- -p 8080:8080 maps your local port 8080 to the container's 8080.

- The server should now be accessible on:

```bash
http://localhost:8080
```

### View Running Containers

```bash
 docker ps
```

### Stop the Container

If you want to stop the running container:

- Find the container ID:

```bash
 docker ps
```

- Then stop it:

```bash
 docker stop [container_id]
```

### Push the Image

```bash
 docker push yourdockerhubusername/go-web-app:v1
```

## 🐾 Troubleshooting

| Issue                         | Cause                       | Solution                                                                        |
| ----------------------------- | --------------------------- | ------------------------------------------------------------------------------- |
| Exited (1) on container start | Binary not found or crashed | Check your Dockerfile, verify the build step, use `docker logs [container_id]`. |
| App inaccessible on browser   | Wrong listen address        | Ensure `http.ListenAndServe(":8080", nil)` is used in `main.go`.                |
| Permissions errors            | Binary is not executable    | Ensure `go build -o main .` properly builds the executable file.                |
