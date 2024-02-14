# Kubernetes Zero Downtime demo
This project is for demonstrating the different elements for setting up zero downtime deployments.

## Summary
This demostrates three different levels of zero downtime deployments
1. Barebones nginx deployment
2. Added readiness and liveness probes
3. Added preStop lifecycle hook with sleep statement

## Requirements
- A local kubernetes cluster, I'll provide instructions for minikube but kind would also work
- kubectl
- kustomize (should be included with kubectl)
- An http load tester, I recommend siege

## Steps
- Start the minikube cluster with `minikube start`
- Install the traefic ingress controller by running the [traefik-install-helm](traefik-install-helm.sh) script
- Connect to the traefik load balancer from localhost by running `minikube tunnel` in a new terminal window
- Apply the base nginx deployment in the k8s folder with `kubectl apply -k k8s`
- Start running siege `siege -v 127.0.0.1 -c 2`
- Modify the nginx [deployment manifest](/k8s/nginx/deploy.yaml)
- Redeploy with `kubectl apply -k k8s` and monitor the HTTP responses, you should see multiple 502 responses as the pods are terminated and started
- Add the readiness / liveness probes to the deployment by running `kubectl apply -k kustom-probes`
- Modify the nginx [deployment manifest](/k8s/nginx/deploy.yaml) again and redeploy, you should see less 502 responses
- Add the prestop lifecycle hook to the deploy by running `kubectl apply -k kustom-prestop-hook`
- Modify the nginx [deployment manifest](/k8s/nginx/deploy.yaml) one more time and redeploy, you should see all 200 responses!