# syntax=docker/dockerfile:1

FROM golang:1.19
WORKDIR /app
COPY main.go ./
EXPOSE 3333
CMD ["go", "run", "main.go"]
