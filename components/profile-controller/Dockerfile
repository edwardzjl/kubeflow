# Build the manager binary
FROM golang:1.17 as builder

WORKDIR /workspace
# Copy the Go Modules manifests
COPY go.mod go.mod
COPY go.sum go.sum
# cache deps before building and copying source so that we don't need to re-download as much
# and so that source changes don't invalidate our downloaded layer
RUN go mod download

# Copy the go source
COPY main.go main.go
COPY api/ api/
COPY controllers/ controllers/
RUN cp /bin/dash /workspace/dash

# Build
RUN CGO_ENABLED=0 GOOS=linux GO111MODULE=on go build -a -o manager main.go;

# Use distroless as minimal base image to package the manager binary
# Refer to https://github.com/GoogleContainerTools/distroless for more details
FROM gcr.io/distroless/static:nonroot as serve

WORKDIR /
COPY --from=builder /workspace/dash /bin/dash
COPY third_party third_party
COPY --from=builder /workspace/manager .
COPY --from=builder /go/pkg/mod/github.com/hashicorp third_party/library/

USER 65532:65532

EXPOSE 8080

ENTRYPOINT ["/manager"]

