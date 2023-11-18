# week-9-1019
NutBodyslam053@TCC231019

ingress.yaml:
```bash
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress-tcc231019
  namespace: group-6
  annotations:
    nginx.ingress.kubernetes.io/backend-protocol: HTTP
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  ingressClassName: nginx
  rules:
  - host: tcc-01.th1.proen.cloud
    http:
      paths:
      - path: "/group-6/week-9-tcc231001(/|$)(.*)"
        pathType: Prefix
        backend:
          service:
            name: my-service-tcc231019
            port:
              number: 80
```

service.yaml:
```bash
apiVersion: v1
kind: Service
metadata:
  name: my-service-tcc231019
  namespace: group-6
  labels:
    app: my-app-tcc231019
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```
deployment.yaml:
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment-tcc231019
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app-tcc231019
  template:
    metadata:
      labels:
        app: my-app-tcc231019
    spec:
      containers:
      - name: my-container-tcc231019
        image: nutbodyslam053/ci:e0009de55ab757e6dc112f7b29679d55893ff088
        imagePullPolicy: "IfNotPresent"
        ports:
        - containerPort: 80

        envFrom:
        - secretRef:
          name: my-secret-tcc231019
        - configMapRef:
          name: my-configmap-tcc231019
```
configmap.yaml:
```bash
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-configmap-tcc231019
  namespace: group-6
data:
  APP_CONFIG: |
    GH_TOKEN: "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"
    key2: value2
    key3: value3
```
secret.yaml:
```bash
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
  namespace: group-6
type: Opaque
data:
  username: dXNlcm5hbWU=  # base64-encoded username
  password: cGFzc3dvcmQ=  # base64-encoded password
stringData:
  ConnectionStrings__Default: Server=mysql-service;Port=3306;Database=myDataBase;Uid=myUsername;Pwd=myPassword;
```