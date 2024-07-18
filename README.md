Dockerfiler : 
```
FROM python:3.10-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

MANIFEST : 

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
      nodePort: 30081
  type: ClusterIP

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
Deploiement redis : 

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-deployment
spec:
  selector:
    matchLabels:
      app: redis
  replicas: 1
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis
        image: redis:alpine
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

```

Pod en run : 
```
lassay@Windows11:~/python_redis$ k get pod
NAME                                        READY   STATUS    RESTARTS        AGE
curlpod                                     0/1     Error     0               3d2h
fastapi-deployment-6c9fdf778b-l2lf5         1/1     Running   2 (4m18s ago)   3d2h
fastapi-redis-deployment-585bc6b778-ssjcn   1/1     Running   2 (4m18s ago)   3d4h
redis-deployment-6d67947767-h2hzn           1/1     Running   2 (4m18s ago)   3d3h
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
