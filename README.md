This image is deprecated. Use the Kubernetes builtin grpc health probing.

https://kubernetes.io/blog/2022/05/13/grpc-probes-now-in-beta/

# grpc-health-probe-docker-image

Docker image of https://github.com/grpc-ecosystem/grpc-health-probe. The image
is available at https://hub.docker.com/r/anipos/grpc-health-probe.

You can use this image as a sidecar to check liveness/readiness of gRPC
servers. Here is the snippet of Kubernetes manifest.

```
spec:
  template:
    spec:
      containers:
        - name: grpc-server
          image: whatever/grpc-server
          ports:
            - name: grpc
              containerPort: 9090
        - name: grpc-health-probe
          image: anipos/grpc-health-probe:latest
          # To keep this image alive. See
          # https://stackoverflow.com/a/35770783/2597718
          command: ["sh"]
          args:
            - "-c"
            - "trap : TERM INT; (while true; do sleep 3600; done) & wait"
          livenessProbe:
            exec:
              command: ["/bin/grpc_health_probe", "-addr=:9090"]
          readinessProbe:
            exec:
              command: ["/bin/grpc_health_probe", "-addr=:9090"]
```
