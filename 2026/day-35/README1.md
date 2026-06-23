Day 35 – Multi-Stage Builds & Docker Hub
Objective

Learn how real teams create small, secure images using multi-stage builds and prepare them for Docker Hub distribution.

Project Structure
day-35/
│
├── app/
│   └── main.go
│
├── Dockerfile.multi
└── day-35-multistage-hub.md
Application

Simple Go web server:

package main

import (
    "fmt"
    "net/http"
)

func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Hello from Day 35 Multi-Stage Build!")
    })

    http.ListenAndServe(":8080", nil)
}
Why Go Doesn't Need requirements.txt

Python:

requirements.txt
↓
pip install

Go:

main.go
↓
go compiler
↓
single binary

Since only built-in libraries were used (fmt, net/http), no external dependencies or go.mod file were required.

Multi-Stage Build
Builder Stage
FROM golang:1.25 AS builder

WORKDIR /src

COPY app/main.go .

RUN CGO_ENABLED=0 GOOS=linux go build -o /hello main.go
Explanation

Move into:

/src

Copy source code:

main.go

Compile application:

main.go
↓
go build
↓
/hello

CGO disabled:

CGO_ENABLED=0

Creates a static Linux binary that Alpine can run.

Runtime Stage
FROM alpine:3.22

COPY --from=builder /hello /hello

EXPOSE 8080

CMD ["/hello"]
Explanation

Start with tiny Alpine Linux.

Copy only:

/hello

from builder stage.

No compiler.

No source code.

No unnecessary packages.

Run:

/hello

when container starts.

Complete Dockerfile
FROM golang:1.25 AS builder

WORKDIR /src

COPY app/main.go .

RUN CGO_ENABLED=0 GOOS=linux go build -o /hello main.go

FROM alpine:3.22

COPY --from=builder /hello /hello

EXPOSE 8080

CMD ["/hello"]
Build Image
docker build -f Dockerfile.multi -t day35-go-app:v1 .
Verify Image
docker images

Output:

day35-go-app:v1
Run Container
docker run -d \
--name day35-go \
-p 8080:8080 \
day35-go-app:v1

Verify:

docker ps
Test

Browser:

http://localhost:8080

Output:

Hello from Day 35 Multi-Stage Build!
How Multi-Stage Works
Stage 1 (Builder)

golang image
     │
     ▼
 main.go
     │
     ▼
 go build
     │
     ▼
 /hello binary
     │
     └─────────────┐
                   ▼

Stage 2 (Runtime)

alpine image
     │
     ▼
copy /hello
     │
     ▼
small production image
     │
     ▼
run /hello
Why Multi-Stage Is Better

Without multi-stage:

Go compiler
Source code
Build tools
Caches
Libraries

all ship inside image.

With multi-stage:

Only binary
+
Tiny Alpine Linux

Result:

Smaller image
Faster pull
Lower attack surface
Better security
Production-ready
Troubleshooting
Error
exec ./hello: no such file or directory
Cause

Binary wasn't copied correctly or was dynamically linked.

Solution

Compile static binary:

RUN CGO_ENABLED=0 GOOS=linux go build -o /hello main.go

Use absolute path:

CMD ["/hello"]
Container Exits Immediately

Check logs:

docker logs day35-go

Verify image:

docker images

Verify container:

docker ps -a
DevOps Concepts Learned
Multi-stage builds
Builder stage vs runtime stage
Static binaries
Alpine Linux
COPY --from
Image optimization
EXPOSE
CMD
Container troubleshooting
Docker logs
Minimal images
Production image best practices
Resume Bullet

Built and containerized a Go web application using Docker multi-stage builds, producing lightweight production images with Alpine Linux and static binaries. Implemented image optimization and container troubleshooting practices commonly used in CI/CD and production environments.
