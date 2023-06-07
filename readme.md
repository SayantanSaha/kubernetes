1. Create k3d managed local registry

```
    k3d registry create myregistry.localhost --port 5000
```

2. Create k3d Cluster 
```
    k3d cluster create newcluster --servers 3 --agents 3 --registry-use k3d-myregistry.localhost:5000
```

3. expose cluster port 80 to host port 8081

```
    k3d cluster edit newcluster --port-add 8081:80@loadbalancer
```

4. Create and push the docker image for go app 

```
    cd go
    docker build -t k3d-myregistry.localhost:5000/go-app .
    docker push k3d-myregistry.localhost:5000/go-app
```
5. Create the deployment for the go app

```
    kubectl create deployment go-app --image k3d-myregistry.localhost:5000/go-app
```

6. Create service for the go app

```
kubectl apply -f service.yaml
```

Note the anotations in service.yaml
```
    traefik.ingress.kubernetes.io/rule-type: PathPrefixStrip
    traefik.ingress.kubernetes.io/rule-value: "/go"
```
These tell the ingress to strip the path prefix before forwarding the request to the service

7. Repeat steps 4-6 for the node app also

8. Apply the ingress

```
    kubectl apply -f ingress.yaml
```

9. test go service

```
curl -v -X POST 'http://localhost:8081/go/hello'
```

10. test node service

```
    curl --request POST \
  --url http://localhost:8081/node/test \
  --header 'content-type: application/json' \
  --data '{"msg": "testing" }'

```

```
    curl http://localhost:8081/node/test
```

