# Proposed Architecture with EKS

## Application building process:
   In order to reduce toiling, boost security and reduce the running container size I decided to split the original Docker file in two different steps:
      1. Compile the application code using GitHub actions + semantic version and push it to an S3 bucket. 
           The reason behind this is because due to the installation of compilers as root and the different layers it will not only increase the container image size, but is a security risk for a production environment.
      2. Use a smaller image like: debian:11-slim or debian:12-slim which is 70MB less then ubuntu. Update the container, create a user with low permissions and use the app in the user space (if the app is not using reserved ports). Create a 
           robust app startup. Remove the apt-cache and all of this in with two layers. Build and tag the container using the same version as the app. Scan it for any CVE’s using trivy, load it to a docker registry: Quay, ECR, DockerHub, etc.
   All this process is done with GitHub actions and PR approvals. There is are some GitHub examples I manage to create for the semantic versioning and a list that need to be answered when you create a PR.


## AWS architecture:
  The proposed solution is to move to 3 AZ from 2 AZ, with 3 private subnets and 3 sg on the cluster side. With two Auto Scaling Groups (ASG): One on-demand and one with a spot instances fleet to reduce costs. One public subnet containing an ALB for redirecting additional context paths and a WAF for securing external perimeter. Finally an Internet Gateway for an external IP. Also some route53 DNS entries and two buckets: one for app storage and one for the Terraform state file.

## EKS architecture: 
   The k8s internal architecture remains minimal, ArgoCD to install all additional services with Kyverno for enforcing k8s policy’s, Istio + kiali + cert manager + True Crypt as service mesh will secure internal traffic encryption. CNI will facilitate pod IP assignment and create an automatic ENI. Keda and Karpenter are event driven auto-scalers which in combination with HPA it can face unpredictable traffic peaks scaling pods and nodes. The ingress gateway and HAProxy will be very helpful to expose specific context paths and facilitate SVC redirects. Grafana + Loki + YACE + OpenTelemetry + Prometheus (or Thanos because it’s HA), will cover the Observability, Logging and Alerting.

## NOTE:
  I didn’t talked about IAM and roles, because that is something that is necessary to do when you know what your application does exactly and whom needs to interact with permissions and clusters.

       

