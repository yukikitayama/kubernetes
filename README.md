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
