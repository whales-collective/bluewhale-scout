# Stage 1: Build the Go application
FROM golang:1.25.3-alpine AS builder

WORKDIR /app

# Copy go mod and sum files
COPY go.mod go.sum ./
# Download all dependencies. Dependencies will be cached if the go.mod and go.sum files are not changed
RUN go mod download

# Copy the source code
COPY . .

# Build the application
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o /go/bin/hello .

# Stage 2: Create the final, minimal image
FROM alpine:latest

# Create a non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Copy the static assets and the binary from the builder stage, and set ownership
COPY --from=builder --chown=appuser:appgroup /app/index.html /index.html
COPY --from=builder --chown=appuser:appgroup /go/bin/hello /hello

# Switch to the non-root user
USER appuser

# Expose port 8080
EXPOSE 8080

# Run the binary
CMD ["/hello"]
