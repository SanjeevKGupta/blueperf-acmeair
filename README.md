# blueperf-acmeair

To clone, build, push and deploy Acme Air micro-services app

- [Prepare source repos by cloning various repos](#prepare-source-repos)
- [Build and push images](#build-and-push-images)
- [Deploy the application - Two variations](#deploy-the-application)
  - [Each service and corresponding database together in one namespace](#each-service-with-its-database)
  - [All services in one namespace and all databases in one namespace](#all-services-and-all-databases)   
- [View application in the browser](#view-application-in-the-browser)
### Prepare source repos
1. Git clone this repo
```
git clone https://github.com/SanjeevKGupta/blueperf-acmeair.git
```
2. Git clone original acme repo or your own forked repo in `blueperf-acmeair`
```
cd blueperf-acmeair

git clone https://github.com/blueperf/acmeair-authservice-java.git
git clone https://github.com/blueperf/acmeair-bookingservice-java.git
git clone https://github.com/blueperf/acmeair-customerservice-java.git
git clone https://github.com/blueperf/acmeair-flightservice-java.git
git clone https://github.com/blueperf/acmeair-mainservice-java.git
```
3. Setup ENV as per your project
```
# An arbitrary value to organize your image name
export EDGE_OWNER=<sn.cedge>
# An arbitrary value to organize your image name
export EDGE_DEPLOY=<ex.mesh.rhsi.acmeair>

# The architecture of the worker nodes
export ARCH=amd64

### Authenticated IBM CR access ###
export CR_HOST=<cr-host>
export CR_HOST_USERNAME=cr-host-username>
export CR_HOST_NAMESPACE==cr-host-namespae>
export CR_APP_API_KEY_RO_PULL=<cr-RO-api-key>
export CR_APP_API_KEY_RW_PUSH=<cr-RW-api-key>
```
### Build and push images
4. Build and push images into your authenticated container image registry. Each build takes about 10-15 mts. 
```
cd mesh/rhsi/publish
make build-push-authservice
make build-push-bookingservice
make build-push-customerservice
make build-push-flightservice
make build-push-mainservice
```
### Deploy the application
If you are deploying directly, you will still need to setup above ENVIRONMENT variables for the `make` command to work. Reach out to me for these values.

5. Log into the target K8s cluster
6. Extract `KUBECONFIG` and export
```
kc config view --raw --minify > kubeconfig-<cluster>.yaml
export KUBECONFIG=kubeconfig-<cluster>.yaml
```
7. Deploy the services
This will create all the k8s objects - deploy, pod, services etc.
- `mainservice` provides the ingress URL used by the app.
- `mainservice` and `authservice` do not have backend databases. Other three services have.

#### Each service with its database
Each service and corresponding database together in one namespace each.
```
make deploy-mainservice NAMESPACE=zz-test-main
make deploy-authservice NAMESPACE=zz-test-auth
make deploy-bookingservice-db NAMESPACE=zz-test-booking
make deploy-customerservice-db NAMESPACE=zz-test-customer
make deploy-flightservice-db NAMESPACE=zz-test-flight
```
#### All services and all databases
All services in one namespace and all databases in one namespace. `mainservice` (UI) in a seperate namespace.
```
make deploy-mainservice NAMESPACE=zz-test-grp-main

make deploy-authservice NAMESPACE=zz-test-gro-service
make deploy-bookingservice NAMESPACE=zz-test-gro-service
make deploy-customerservice NAMESPACE=zz-test-gro-service
make deploy-flightservice NAMESPACE=zz-test-gro-service
make deploy-booking-db NAMESPACE=zz-test-gro-service
make deploy-customer-db NAMESPACE=zz-test-gro-service
make deploy-flight-db NAMESPACE=zz-test-gro-service

make deploy-booking-db NAMESPACE=zz-test-gro-db
make deploy-customer-db NAMESPACE=zz-test-gro-db
make deploy-flight-db NAMESPACE=zz-test-gro-db
```
### View application in the browser
8. Get the `ingress` from the namespace where `mainservice` is running 
```
kubectl get ing -n <mainservice-namespace>
```
9. Access using ingress-url with path as `/acmeair`
```
http://<ingress-url>/acmeair/
```
10. Login using the UI. Use the default as prompted.
11. Initialize various databses using the UI or CLI
```
curl http://acmeair.apps.your.clusterhost.com/booking/loader/load
curl http://acmeair.apps.your.clusterhost.com/flight/loader/load
curl http://acmeair.apps.your.clusterhost.com/customer/loader/load?numCustomers=10000
```
Reference: https://github.com/blueperf/acmeair-mainservice-java



