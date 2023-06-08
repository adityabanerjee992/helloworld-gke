
To deploy a Node js app on Google Cloud Kubernetes Engine (GKE), you can follow these steps:

1. Install the Google Cloud CLI and initialize it by running the following command:

   ```
   gcloud init
   ```
2. Install `kubectl`, which is used to manage Kubernetes, by using the following command:

   ```
   gcloud components install kubectl
   ```
3. Create a new directory named `helloworld-gke` and navigate into it:

   ```
   mkdir helloworld-gke
   cd helloworld-gke
   ```
4. Create a `package.json` file with the specified contents in the current directory.
5. Create an `index.js` file with the provided code in the current directory.
6. Create a `Dockerfile` in the current directory with the specified content.
7. Create a `.dockerignore` file to exclude certain local files from affecting the container build process.
8. Retrieve your Google Cloud project ID using the following command:

   ```
   gcloud config get-value project
   ```
9. Create a repository named `hello-repo` in Artifact Registry by executing the command below, replacing `PROJECT_ID` with your actual project ID:

   ```
   gcloud artifacts repositories create hello-repo --project=PROJECT_ID --repository-format=docker --location=us-central1 --description="Docker repository"
   ```
10. Build your container image using Cloud Build by running the following command, replacing `PROJECT_ID` with your project ID:

```
   gcloud builds submit --tag us-central1-docker.pkg.dev/PROJECT_ID/hello-repo/helloworld-gke .
```

11. Create a GKE cluster using the command:

```
   gcloud container clusters create-auto helloworld-gke --region us-central1
```

12. Verify your access to the cluster by checking the nodes:

```
   kubectl get nodes
```

13. Deploy the app to the GKE cluster by creating a deployment with the specified YAML file:

```
   kubectl apply -f deployment.yaml
```

14. Monitor the status of the deployment:

```
   kubectl get deployments
```

15. Deploy a service to define how to access your app using the provided YAML file:

```
   kubectl apply -f service.yaml
```

16. Obtain the external IP address of the service:

```
   kubectl get services
```

17. Access the deployed app by visiting the external IP address in a web browser or making a curl call to the IP address.

These steps outline the process of deploying a Node app on GKE. Make sure to replace any placeholder values with the appropriate information from your environment.

deployment.yaml:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: helloworld-gke
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hello
  template:
    metadata:
      labels:
        app: hello
    spec:
      containers:
      - name: hello-app
        # Replace $LOCATION with your Artifact Registry location (e.g., us-west1).
        # Replace $GCLOUD_PROJECT with your project ID.
        image: $LOCATION-docker.pkg.dev/$GCLOUD_PROJECT/hello-repo/helloworld-gke:latest
        # This app listens on port 8080 for web traffic by default.
        ports:
        - containerPort: 8080
        env:
          - name: PORT
            value: "8080"
```

service.yaml:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: hello
spec:
  type: LoadBalancer
  selector:
    app: hello
  ports:
  - port: 80
    targetPort: 8080
```

Make sure to replace `$LOCATION` with your Artifact Registry location (e.g., us-west1) and `$GCLOUD_PROJECT` with your actual project ID. These YAML files define the deployment and service resources for your Hello World app on GKE.
