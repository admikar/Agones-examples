# Agones-examples

Agones is a framework designed to deploy, run, and scale game servers on Kubernetes., Please see the official website and GitHub for more information.

# Install Agones

1.  First, create the required Agones namespace:

    ``` kubectl create namespace agones-system ```

2.  Install the corresponding version of Agones through the configuration file.
   
    ``` kubectl apply -f https://raw.githubusercontent.com/googleforgames/agones/release-1.23.0/install/yaml/install.yaml ```
   
3.  After the installation, the following resources are available in the namespace:

    ``` kubectl get deploy -n agones-system
        kubectl get po -n agones-system
        kubectl get svc -n agones-system
    ```
# Deploy a Game Server on Agones

The Unity example (path: agones/examples/unity-sample) in the official Agones GitHub to import the project to Unity Editor. Build server and client separately.

Use the Dockerfile in the project to create a service image:

```
FROM ubuntu:18.04

WORKDIR /unity

# Need to build with UnityEditor in advance.
COPY Builds/Server/ ./

# [Workaround] Wait until the sidecar is ready.
CMD sleep 2 && ./UnitySimpleServer.x86_64
```
After uploading it to the image repository, we start to deploy the game server.

```
apiVersion: "agones.dev/v1"
kind: GameServer
metadata:
  name: "unity-simple-server"
spec:
  ports:
  - name: default
    portPolicy: Dynamic
    containerPort: 7777
  template:
    spec:
      containers:
      - name: unity-simple-server
        #Use the game server image just created.
        image: xxx:xx
```

Soon, gs will complete the deployment.
```
kubectl get gs
```
We use the client to connect to the game server through ADDRESS and PORT.
