# Introduction 

This repository provides guidance on configuring Kubernetes with Traefik to set up OAuth/SSO login and authentication via the Traefik reverse proxy/load balancer. It's designed for those looking to secure their Kubernetes applications with an authentication layer.

# Prerequisite

Before starting, ensure you have the following:

- AKS or Kubernetes Cluster: A running instance of Azure Kubernetes Service (AKS) or any Kubernetes cluster.
- Authentication Details: Obtain the following from your authentication provider:
  - Issuer URL: The URL of your authentication provider.
  - Client ID: A unique identifier for your application.
  - Client Secret: A secret key used for authentication with your provider.
  - JWT Secret: A secret key for signing JSON Web Tokens. This can be a randomly generated string.
  - Domain: The domain name where your service is hosted.


# Setup Treafik ingress

To configure Traefik as your ingress controller, follow these steps:

1.Add the Traefik Helm chart repository and update it:

```
helm repo add traefik https://helm.traefik.io/traefik
helm repo update
```
2.Install Traefik using the provided traefik-values.yml configuration file:

```
helm install traefik traefik/traefik -f "./traefik-values.yml" -n traefik --create-namespace
```
![traefik-values.yml](./traefik-values.yml)

# Setup Auth Proxy
Deploy an authentication proxy using thomseddon/traefik-forward-auth:latest. This service will act as an intermediary, handling authentication requests for your applications.

1.Configuration: Use the previously mentioned authentication details to configure the proxy.

2.Deployment: The proxy will be accessible at traefikauth.my-domain.com and will route internally to http://traefikauth:4181.

# Setup and Deploy Sample App
To secure your sample application with the authentication layer, perform the following:

1. Middleware Creation: Create middleware to forward all traffic to the auth proxy, ensuring every request is authenticated.
   - ![Middleware](./middleware-auth.yml)
   
3. Kubernetes Resources: Define and deploy the necessary Kubernetes resources for your application, including:
  - Deployment![Deployment](./sampleapp/deployment.yaml): To manage your application's replicas.
  - Service![Service](./sampleapp/service.yaml): To expose your application within the Kubernetes cluster.
  - Ingress Route![Ingress Route](./sampleapp/ingress.yaml): To define how external traffic is routed to your service, integrating with Traefik for ingress control.
    
Following these steps will secure your application with OAuth/SSO authentication, leveraging Traefik for efficient traffic management and security.



