`cat > serviceA-v1-deployment.yml <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: serviceA-v1-deployment
  labels:
    app: serviceA-app
    version: v1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: serviceA-app
      version: v1
  template:
    metadata:
      labels:
        app: serviceA-app
        version: v1
    spec:
      containers:
      - name: serviceA-container
        image: artashesavetisyan/istio-basics-course:service-a
        imagePullPolicy: Always
        ports:
        - containerPort: 8081
        securityContext:
          runAsUser: 1000
EOF`{{execute}}

`kubectl apply -f serviceA-v1-deployment.yml`{{execute}}



https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/deployment-v1/
https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/pod-v1/#Container
