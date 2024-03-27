# Kubernetes

## FastAPI

FastAPI https://fastapi.tiangolo.com/#example

Deploy to Kubernetes
- https://fastapi.tiangolo.com/deployment/docker/
- https://sumanta9090.medium.com/deploying-a-fastapi-application-on-kubernetes-a-step-by-step-guide-for-production-d74faac4ca36

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
  