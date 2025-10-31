# HOW TO RUN KUBERNETES IN YOUR LOCAL SYSTEM & SOME BASICS (MAC EDITION)


1. You need to create docker image and run that in a docker contianer. but it wont support a docker container that runs on your local docker.
2. So you need a K8s Cluster, and there's something called: "minikube" that you can install and it has its own docker daemon and shit so easy for you to take docker containers here and then apply k8s.

install minikube:

```TERMINAL
brew install minikube
```

- and then create a basic express app.
- now add a docker file and write the recipie

```js

FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "index.js"]

```

- now start minikube docker in you mac.

```
eval $(minikube docker-env)
docker build -t image-name .
```

now you must have a docker image runnning.

- create a service and a deployment yaml file in your express servers folders.

deployment.yaml:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: express-deployment
  
spec:
  replicas: 3
  selector:
    matchLabels:
      app: express-app
  template:
    metadata:
      labels:
        app: express-app
    spec:
      containers:
      - name: express-container
        image: express-k8s-demo
        imagePullPolicy: Never
        ports:
          - containerPort: 3000

```


this usually contians stuff like:
1. your restart policy: [never(dont restart even if it fails),always(always do it)]
2. how many replicas, where to find it and shit.

---

service.yaml

```
apiVersion: v1
kind: Service
metadata:
  name: express-service
spec:
  selector:
    app: express-app
  ports:
    - port: 80
      targetPort: 3000
  type: LoadBalancer

```

this can usually be your netwoking side of things, where you can make it a load balancer talk about port adn shit!


and thats it then:

```
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

this applies and now check if the pods are created:

```
kubectl get pods
#delete them by kubectl delete pods --all, etc etc...

# to access the thingy or see where your express is:

kubectl get svc
and open the link done
```

