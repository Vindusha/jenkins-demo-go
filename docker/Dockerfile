# Start from golang base image
FROM golang:1.13.5-stretch

# Set the current working directory inside the container 
WORKDIR /app

# Copy go mod and sum files 
COPY go.mod ./

# Download all dependencies. Dependencies will be cached if the go.mod and the go.sum files are not changed 
RUN go mod download 

# Copy the source from the current directory to the working Directory inside the container 
COPY . .

# Build the Go app
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o main .

# # Start a new stage from scratch
# FROM containers.cisco.com/sto-ccc-cloud9/hardened_alpine:latest
# RUN apk --no-cache add ca-certificates

# WORKDIR /app

# # Copy the Pre-built binary file from the previous stage. Observe we also copied the .env file
# COPY --from=builder /app/config/ config/
# COPY --from=builder /app/main .

#Command to run the executable
CMD ["./main"]