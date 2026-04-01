# Docker 101

This project is a simple Go web server that serves a single HTML page.

### How to run it

```bash
go run main.go
```
Then, open your browser and go to http://localhost:8080.

## Dockerizing the application

### The Dockerfile

The `Dockerfile` uses a multi-stage build to create a small and efficient Docker image.

**Stage 1: The Builder**

```dockerfile
FROM golang:1.22-alpine AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o /go/bin/hello .
```

-   `FROM golang:1.22-alpine AS builder`: We start with the official Go image on Alpine Linux and name this stage `builder`.
-   `WORKDIR /app`: We set the working directory to `/app`.
-   `COPY go.mod go.sum ./`: We copy the module files first to leverage Docker's build cache. The dependencies will only be re-downloaded if these files change.
-   `RUN go mod download`: We download the dependencies.
-   `COPY . .`: We copy the rest of the source code.
-   `RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o /go/bin/hello .`: We build the Go application.
    -   `CGO_ENABLED=0`: This disables CGO, which is needed for cross-compilation and to create a static binary.
    -   `GOOS=linux`: We specify the target operating system as Linux.
    -   The output is a single binary file named `hello` in the `/go/bin/` directory.

**Stage 2: The Final Image**

```dockerfile
FROM alpine:latest
COPY --from=builder /app/index.html /index.html
COPY --from=builder /go/bin/hello /hello
EXPOSE 8080
CMD ["/hello"]
```

-   `FROM alpine:latest`: We use the lightweight `alpine:latest` image for our final image.
-   `COPY --from=builder /app/index.html /index.html`: We copy the `index.html` file from the `builder` stage.
-   `COPY --from=builder /go/bin/hello /hello`: We copy the compiled binary from the `builder` stage.
-   `EXPOSE 8080`: We expose port 8080.
-   `CMD ["/hello"]`: We set the command to run when the container starts.

### How to build the image

```bash
docker build -t hello-go:demo .
```

### How to run the container

```bash
docker run -p 7070:8080 hello-go:demo
```

Then, open your browser and go to http://localhost:7070.