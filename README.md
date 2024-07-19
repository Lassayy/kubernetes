```
vim dockerfile
```

Mettre le contenue ci dessous :

```
FROM python:3.10-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

Builder et pusher sur le dockerhub :

Mettre ses identifiant de docker hub
```
docker login
```

Pour Builder une fois connecté

```
docker build -t lassay/fastapi-redis-app .
```
Pour pusher sur le dockerhub
```
docker push lassay/fastapi-redis-app
```

Lien docker hub :
```
https://hub.docker.com/layers/lassay/fastapi-redis-app/latest/images/sha256-0f17bccb3f69e1137ac0c91a6e958881f898477ec8153f59ab77e2e2247a9e0a?context=repo
```

Voici l'exemple dans mon cas :

```
lassay@Windows11:~/python_redis$ docker build -t lassay/fastapi-redis-app .
[+] Building 10.7s (11/11) FINISHED                                                                                                        docker:default
 => [internal] load build definition from dockerfile                                                                                                 0.0s
 => => transferring dockerfile: 236B                                                                                                                 0.0s
 => [internal] load metadata for docker.io/library/python:3.10-slim                                                                                  1.7s
 => [auth] library/python:pull token for registry-1.docker.io                                                                                        0.0s
 => [internal] load .dockerignore                                                                                                                    0.0s
 => => transferring context: 2B                                                                                                                      0.0s
 => [1/5] FROM docker.io/library/python:3.10-slim@sha256:3be54aca807a43b5a1fa2133b1cbb4b58a018d6ebb1588cf1050b7cbebf15d55                            3.9s
 => => resolve docker.io/library/python:3.10-slim@sha256:3be54aca807a43b5a1fa2133b1cbb4b58a018d6ebb1588cf1050b7cbebf15d55                            0.0s
 => => sha256:de7ad0443e19e374770dc29d7c7677a2f9e2162918003747266fd3b4f983cd85 6.92kB / 6.92kB                                                       0.0s
 => => sha256:f11c1adaa26e078479ccdd45312ea3b88476441b91be0ec898a7e07bfd05badc 29.13MB / 29.13MB                                                     1.9s
 => => sha256:e9e504b3ee6224b21b006ddf334064b7764c2aadcc9d8be012cf44640f045483 3.51MB / 3.51MB                                                       1.4s
 => => sha256:455661270fd3aeac7e92f2eabcb2b9fd03c0f97dcc26aef6889555cb9ebc23a4 12.38MB / 12.38MB                                                     2.4s
 => => sha256:3be54aca807a43b5a1fa2133b1cbb4b58a018d6ebb1588cf1050b7cbebf15d55 9.13kB / 9.13kB                                                       0.0s
 => => sha256:8244f266195f442379acd0e7d8918b1ef3967050ebf49c40291e89d0a1ba8f94 1.94kB / 1.94kB                                                       0.0s
 => => sha256:8c0cd2ed72c6f636fe88f580adf3f0c5f5efd9254dc3feece396ac26fe3b8eaf 231B / 231B                                                           1.8s
 => => sha256:3d2f440aae987a194992068ec8c982c0295ac62537989c70aeccce6fa1548fd6 3.16MB / 3.16MB                                                       2.4s
 => => extracting sha256:f11c1adaa26e078479ccdd45312ea3b88476441b91be0ec898a7e07bfd05badc                                                            1.0s
 => => extracting sha256:e9e504b3ee6224b21b006ddf334064b7764c2aadcc9d8be012cf44640f045483                                                            0.1s
 => => extracting sha256:455661270fd3aeac7e92f2eabcb2b9fd03c0f97dcc26aef6889555cb9ebc23a4                                                            0.3s
 => => extracting sha256:8c0cd2ed72c6f636fe88f580adf3f0c5f5efd9254dc3feece396ac26fe3b8eaf                                                            0.0s
 => => extracting sha256:3d2f440aae987a194992068ec8c982c0295ac62537989c70aeccce6fa1548fd6                                                            0.2s
 => [internal] load build context                                                                                                                    0.0s
 => => transferring context: 3.01kB                                                                                                                  0.0s
 => [2/5] WORKDIR /app                                                                                                                               0.1s
 => [3/5] COPY requirements.txt .                                                                                                                    0.0s
 => [4/5] RUN pip install --no-cache-dir -r requirements.txt                                                                                         4.7s
 => [5/5] COPY . .                                                                                                                                   0.0s
 => exporting to image                                                                                                                               0.1s
 => => exporting layers                                                                                                                              0.1s
 => => writing image sha256:4de9b350aa5911fa1d9c3094c47acad46bdf5d1e1f96be9ed3e2b112728ab741                                                         0.0s
 => => naming to docker.io/lassay/fastapi-redis-app                                                                                                  0.0s

What's Next?
  View a summary of image vulnerabilities and recommendations → docker scout quickview
lassay@Windows11:~/python_redis$ docker push lassay/fastapi-redis-app
Using default tag: latest
The push refers to repository [docker.io/lassay/fastapi-redis-app]
98eb76deac16: Pushed 
1a7de6a60322: Pushed 
b79faf77edf0: Pushed 
8eacf40a93ca: Pushed 
c3f9a6a5748b: Mounted from library/python 
bb75f9b0e544: Mounted from library/python 
b0fd3128dcdb: Mounted from library/python 
1fe609a255a3: Mounted from library/python 
32148f9f6c5a: Mounted from library/python 
latest: digest: sha256:beb0f3dcd864ca0b1efbca6625a4f99a360ae3b48b22a9ebe54d25702518f08e size: 2202
```

Création du manifests kubernetes : 

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fastapi-redis-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: fastapi-redis
  template:
    metadata:
      labels:
        app: fastapi-redis
    spec:
      containers:
      - name: fastapi-redis
        image: lassay/fastapi-redis-app:latest
        ports:
        - containerPort: 8000
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
        readinessProbe:
          httpGet:
            path: /
            port: 8000
          initialDelaySeconds: 5
          periodSeconds: 10
        livenessProbe:
          httpGet:
            path: /
            port: 8000
          initialDelaySeconds: 15
          periodSeconds: 20
---
apiVersion: v1
kind: Service
metadata:
  name: fastapi-redis-service
spec:
  selector:
    app: fastapi-redis
  ports:
  - port: 80
    targetPort: 8000
    protocol: TCP
    nodePort: 30080
  type: ClusterIP

```

Deploiement fastapi : 
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fastapi-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: fastapi
  template:
    metadata:
      labels:
        app: fastapi
    spec:
      containers:
      - name: fastapi
        image: lassay/fastapi-redis-app:latest
        env:
        - name: REDIS_HOST
          value: "redis-service"
        ports:
        - containerPort: 8000
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
        readinessProbe:
          httpGet:
            path: /
            port: 8000
          initialDelaySeconds: 5
          periodSeconds: 10
        livenessProbe:
          httpGet:
            path: /
            port: 8000
          initialDelaySeconds: 15
          periodSeconds: 20
---
apiVersion: v1
kind: Service
metadata:
  name: fastapi-service
spec:
  selector:
    app: fastapi
  ports:
    - port: 80
      targetPort: 8000
      protocol: TCP
  type: LoadBalancer
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis
        image: redis:6.2
        ports:
        - containerPort: 6379
---
apiVersion: v1
kind: Service
metadata:
  name: redis-service
spec:
  selector:
    app: redis
  ports:
  - port: 6379
    targetPort: 6379
    protocol: TCP
  type: ClusterIP
```
Pour déployer :
```
kubectl apply -f fastapi-deployment.yaml
```

Pod en run : 

```
lassay@Windows11:~/python_redis$ k get pod
NAME                                        READY   STATUS    RESTARTS   AGE
fastapi-deployment-575c6cff87-tbx7k         1/1     Running   0          18s
fastapi-redis-deployment-79f4487dd5-96bpd   1/1     Running   0          4m32s
redis-deployment-6754c7f8b6-kpd8n           1/1     Running   0          4m32s
```

Vérification :

création de pod temporaire + test 

```
kubectl run -it --rm --restart=Never curlpod --image=curlimages/curl -- sh

$ curl -X POST "http://fastapi-service/set_item/mykey2/myvalue2"
{"message":"Item set successfully","key":"mykey2","value":"myvalue2"}

~ $ curl "http://fastapi-service/get_data_from_redis/mykey2"
{"key":"mykey2","value":"myvalue2"}
```

Question : 
a. Expliquer le choix du workload et du service

```
Workload - Deployment
Le Deployment est choisi pour gérer l'application FastAPI et Redis, il permet une gestion automatique des réplicas, des mises à jour sans interruption et l'auto-récupération des pods en cas de panne, il assure la haute disponibilité.

Service - LoadBalancer - ClusterIP
Un Service de type LoadBalancer pour exposer l'application FastAPI à l'extérieur du cluster.
Un Service de type ClusterIP pour rendre Redis accessible uniquement au sein du cluster.
```

b. Expliquer les choix faits pour la configuration de l’application.

```
Déploiement :
- Replicas : Une réplique pour commencer, facilement évolutive.
- Ressources : Limites CPU et mémoire définies pour une utilisation efficace.
- Probes : Vérifient la santé de l'application et redémarrent les pods défaillants.

Service :
- Type LoadBalancer pour FastAPI : Expose l'application à l'extérieur du cluster.
- Type ClusterIP pour Redis : Rend Redis accessible uniquement à l'intérieur du cluster.

Variables d'environnement :

- REDIS_HOST : Indique l'adresse du service Redis pour la connexion.
```



c. Documenter les dépendances éventuelles dont l’app a besoin (bases de
données, cache, …)

```
-Redis : L'application utilise Redis comme base de données en mémoire pour stocker et récupérer des données rapidement. Redis est déployé en tant que service au sein du cluster Kubernetes pour être accessible par l'application FastAPI.

-Librairies Python :
FastAPI : Framework web utilisé pour créer l'API.
Redis : Bibliothèque client pour interagir avec le serveur Redis.
Uvicorn : Serveur pour exécuter FastAPI.

```
d. Bien spécifier les requests et les probes

```
- Requests :
Mémoire : 64Mi
CPU : 250m

-Limits :
Mémoire : 128Mi
CPU : 500m


-Probes :
Readiness Probe :
Type : HTTP GET
Path : /
Port : 8000
Initial Delay : 5 secondes
Période : 10 secondes

-Liveness Probe :
Type : HTTP GET
Path : /
Port : 8000
Initial Delay : 15 secondes
Période : 20 secondes
```


