# Kubernetes

## Concept

**Service** maps requests from outside Kuberneters to pods. Service is an object which listens to a port on the Node, and forward the requests from outside on the port, to a port of a pod. For example, if a pod is running a web application that external users visit, service is needed.

With **NodePort**, users request to IP address of a Node with the port on the Node. **TargetPort** is a port on a pod. **Port** is port on the service and this port is connected to TargetPort. **NodePort** is a port on the Node. Only Port is the reuqired parameter. If the Node IP address changes, you need to re-configure this NodePort Service. Not for production, maybe for demo app and something temporary.

**LoadBalancer** is the standard way to expose a service to the internet. But each service to be exposed need to have its own IP address that you need to pay. HTTP, no HTTPS (https://www.cloudflare.com/learning/ssl/why-is-http-not-secure)

**Ingress** allows you to use HTTPS, centralize managing Services for each applications. Single URL allows to route to different services and implement SSL security.
- https://stackoverflow.com/questions/44708272/how-to-access-a-kubernetes-service-through-https
- https://kubernetes.io/docs/concepts/services-networking/ingress/#tls
- https://cloud.google.com/kubernetes-engine/docs/concepts/ingress
- https://www.cortex.io/post/understanding-kubernetes-services-ingress-networking

NortPort, LoadBalancer, or Ingress?
- https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0

**ClusterIP** is another type of Service, but it's not for external access. But if you use **Kuberneters Proxy** and use Kubernetes API, you can access to this Service. This should be for internal use. It's not a way to expose your application to the internet.

## Docker

- **Image** is the packaged files and metadata
- **Container** is the running instance of the image.
- `pip install --no-cache-dir -r requirements.txt` can keep a Docker image as small as possible.
  - https://stackoverflow.com/questions/45594707/what-is-pips-no-cache-dir-good-for

## FastAPI

FastAPI https://fastapi.tiangolo.com/#example

Deploy to Kubernetes
- https://fastapi.tiangolo.com/deployment/docker/
- https://sumanta9090.medium.com/deploying-a-fastapi-application-on-kubernetes-a-step-by-step-guide-for-production-d74faac4ca36

Steps
- Build Docker image
- Run Docker container locally
  - `docker run -p 80:80 <image-name>`
  - Visit `localhost` in Chrome.
- Push the Docker image to a repo
- Deploy Deployment using the image to Kubernetes
  - https://sumanta9090.medium.com/deploying-a-fastapi-application-on-kubernetes-a-step-by-step-guide-for-production-d74faac4ca36
- Deploy Service to Kubernetes

## Next.js

Resources
- https://nextjs.org/docs/pages/building-your-application/deploying#docker-image
- https://nextjs.org/docs/pages/api-reference/next-config-js/output
- https://github.com/vercel/next.js/tree/canary/examples/with-docker

Steps
- Install Node.js
  - `node -v`
  - `npm -v`
- Install Next.js
  - https://nextjs.org/docs/getting-started/installation#automatic-installation
  - `npx create-next-app@latest`
    - npm ERR! enoent ENOENT: no such file or directory, lstat 'C:\Users\yukic\AppData\Roaming\npm'
      - https://stackoverflow.com/questions/25093276/node-js-windows-error-enoent-stat-c-users-rt-appdata-roaming-npm
  - Change the current directory to the created app directory.
- Run the development server
  - `npm run dev`
- Build Docker image
  - Place the `Dockerfile` that Next.js created into the root of the Next.js project
  - Update next config
    - Add `output: "standalone"` to JSON in `next.config` file.
    - https://github.com/vercel/next.js/discussions/60945 
  - `docker build -t <iamge-name> .`
- Test running in Docker container
  - `docker run -p 3000:3000 <image-name>`
- Push the Docker image to Docker Hub
  - `docker push <repo-name>/<image-name>`
- Create Kubernetes Deployment
  - https://clouddevs.com/next/optimizing-for-kubernetes/
  - `kubectl apply -f <deployment>.yaml`
  - or `kubectl create deployment <deployment-name> --image=<docker-hub-repo-name>/<image-name>`
- Expose the deployment to Service
  - https://cloud.google.com/kubernetes-engine/docs/how-to/exposing-apps#creating_a_service_of_type_loadbalancer
  - https://clouddevs.com/next/optimizing-for-kubernetes/
  - Use `type: LoadBalancer`
  - `kubectl apply -f <service>.yaml`
- `kubectl describe svc <service-name>` to get IP address of the webapp exposed
  - `LoadBalancer Ingress:` contains the IP address
- Configure domain name to the service
  - https://cloud.google.com/kubernetes-engine/docs/tutorials/configuring-domain-name-static-ip
  - Reserve a static IP address
    - `gcloud compute addresses create <ip-address-name> --region <region-name>`
    - `gcloud compute addresses describe demo-nextjs-app-ip --region us-west2` checks the actual reserved IP address in `address:`
  - Put the reserved IP address to `loadBalancerIP: <reserved-ip-address>` in Service deployment YAML
  - Go to Doman Name Record system to add A record with the reserved IP address
    - In my case, go to **AWS Route53** and create A-record with the reserved IP address and new subdomain name 
  