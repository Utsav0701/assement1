**Assessment: Use Case Oriented Project**

Use Case Scenario:

Your company is developing a new e-commerce application consisting of
several

microservices: a front-end service, a product catalog service, and an
order processing

service. The goal is to automate the deployment and configuration of
these services across

development, testing, and production environments using Ansible, Docker,
Kubernetes, and Jenkins.

Front-end:

Docker File
```Dockerfile
# Use the official Nginx image from the Docker Hub
FROM nginx:alpine

# Copy the index.html file to the Nginx HTML directory
COPY index.html /usr/share/nginx/html/index.html

# Expose port 80
EXPOSE 80
```

Index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Assesment 1</title>
</head>
<body>
    <h1>Hello, World! From Frontend Branch:Development</h1>
</body>
</html>
```


frontend-kube.yml
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: front-end
spec:
  replicas: 2
  selector:
    matchLabels:
      app: front-end
  template:
    metadata:
      labels:
        app: front-end
    spec:
      containers:
      - name: front-end
        image: utsavshah0305/front-end:latest
        ports:
        - containerPort: 80
 
---
 
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: front-end  # Make sure this matches the labels in the deployment
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```


![](.//media/image1.png)
![](.//media/image2.png)

Backend:

Docker File
```Dockerfile
# Use the official Nginx image from the Docker Hub
FROM nginx:alpine

# Copy the index.html file to the Nginx HTML directory
COPY index.html /usr/share/nginx/html/index.html

# Expose port 80
EXPOSE 80
```

Index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Assesment 1</title>
</head>
<body>
    <h1>Hello, World! From Frontend Branch:production</h1>
</body>
</html>
```


backend-kube.yml
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: back-end
spec:
  replicas: 2
  selector:
    matchLabels:
      app: back-end
  template:
    metadata:
      labels:
        app: back-end
    spec:
      containers:
      - name: back-end
        image: utsavshah0305/back-end:latest
        ports:
        - containerPort: 80
 
---
 
apiVersion: v1
kind: Service
metadata:
  name: nginx-service-back
spec:
  selector:
    app: back-end
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```


![](.//media/image3.png)

![](.//media/image4.png)

Testing:

Docker File
```Dockerfile
# Use the official Nginx image from the Docker Hub
FROM nginx:alpine

# Copy the index.html file to the Nginx HTML directory
COPY index.html /usr/share/nginx/html/index.html

# Expose port 80
EXPOSE 80
```

Index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Assesment 1</title>
</head>
<body>
    <h1>Hello, World! From Frontend Branch:testing</h1>
</body>
</html>
```


testing-kube.yml
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: testing
spec:
  replicas: 2
  selector:
    matchLabels:
      app: testing
  template:
    metadata:
      labels:
        app: testing
    spec:
      containers:
      - name: testing
        image: utsavshah0305/testing:latest
        ports:
        - containerPort: 80
 
---
 
apiVersion: v1
kind: Service
metadata:
  name: nginx-service-testing
spec:
  selector:
    app: testing
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

## ingress.yml

```yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /

spec:
  rules:
  - host: myapp.local
    http:
      paths:
      - path: /front
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80
      - path: /prod
        pathType: Prefix
        backend:
          service:
            name: nginx-service-back
            port:
              number: 80
      - path: /test
        pathType: Prefix
        backend:
          service:
            name: nginx-service-testing
            port:
              number: 80
```
![](.//media/image5.png)

![](.//media/image6.png)
