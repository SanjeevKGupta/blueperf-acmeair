# blueperf-acmeair

To clone, build, push and deploy Acme Air micro-services app

- [Prepare source repos by cloning various repos](#prepare-source-repos)
- [Build and push images](#build-and-push-images)
- [Deploy the application - Two variations](#deploy-the-application)
  - [Each service and corresponding database together in one namespace](#each-service-with-its-database)
  - [All services in one namespace and all databases in one namespace](#all-services-and-all-databases)   
- [View application in the browser](#view-application-in-the-browser)
## Prepare source repos
1. Git clone this repo
```
git clone https://github.com/SanjeevKGupta/blueperf-acmeair.git
```
2. Git clone original acme repo or your own forked repo in `blueperf-acmeair`
```
cd blueperf-acmeair

git clone https://github.com/SanjeevKGupta/acmeair-authservice-java.git
git clone https://github.com/SanjeevKGupta/acmeair-bookingservice-java.git
git clone https://github.com/SanjeevKGupta/acmeair-customerservice-java.git
git clone https://github.com/SanjeevKGupta/acmeair-flightservice-java.git
git clone https://github.com/SanjeevKGupta/acmeair-mainservice-ja
```
3. Setup ENV as per your project
```
# An arbitrary value to organize your image name
export EDGE_OWNER=<sn.cedge>
# An arbitrary value to organize your image name
export EDGE_DEPLOY=<ex.mesh.rhsi.acmeair>

# The architecture of the worker nodes
export ARCH=amd64

## Authenticated IBM CR access ###
export CR_HOST=<cr-host>
export CR_HOST_USERNAME=cr-host-username>
export CR_HOST_NAMESPACE==cr-host-namespae>
export CR_APP_API_KEY_RO_PULL=<cr-RO-api-key>
export CR_APP_API_KEY_RW_PUSH=<cr-RW-api-key>
```
## Build and push images
4. Build and push images into your authenticated container image registry. Each build takes about 10-15 mts. 
```
cd mesh/rhsi/publish
make build-push-authservice
make build-push-bookingservice
make build-push-customerservice
make build-push-flightservice
make build-push-mainservice
```
## Deploy the application
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

### All services and all databases
All services in one namespace and all databases in one namespace. Namespace cna be on different clouds/clusters

8. Start with deploying databases in one namespace
```
make deploy-booking-db NAMESPACE=sn-acmeair-databases CLUSTER_TYPE=ROKS
make deploy-customer-db NAMESPACE=sn-acmeair-databases CLUSTER_TYPE=ROKS
make deploy-flight-db NAMESPACE=sn-acmeair-databases CLUSTER_TYPE=ROKS
```
- Verify all K8s objects created 
```
kc get all -n sn-acmeair-databases
```

10. Install oh agent
- Verify oh agent is installed

11. In UI register a GW with this oh agent
- Let skupper pods be deployed 
- Let the database services be discovered 

12. Create policy for each of the three DBs
13. Switch to other cluster and update KUBECONFIG. Now deploy application services in another namespace in a cluster
```
make deploy-mainservice NAMESPACE=sn-acmeair-services CLUSTER_TYPE=K8S
make deploy-authservice NAMESPACE=sn-acmeair-services CLUSTER_TYPE=K8S
make deploy-bookingservice NAMESPACE=sn-acmeair-services CLUSTER_TYPE=K8S
make deploy-customerservice NAMESPACE=sn-acmeair-services CLUSTER_TYPE=K8S
make deploy-flightservice NAMESPACE=sn-acmeair-services CLUSTER_TYPE=K8S
```
Note: Booking, customer and flight services will continue to fail to got RUNNING as they need corresponding DBs and that will be provided by Mesh policy later. So at this time this is OK and proceed.
14 Install oh agent
- Verify oh agent is installed
15. In UI register a GW with this oh agent
- Let skupper pods be deployed
- Let the application services be discovered
  
16. Connect the service GW to database GW as outbound.

Note: Soon application services will go RUNNING as they will now have access to DBs via Mesh policy.
```
kubectl get pods -n sn-acmeair-services
```

## View application in the browser
17. K8s (IKS) Get the `ingress` from the namespace where `mainservice` is running 
```
kubectl get ing -n <mainservice-namespace>
```
18. Access using ingress with path as `/acmeair`
```
http://<url>/acmeair/
```
19. Login using the UI. Use the default as prompted.
20. Initialize various databses using the UI or CLI
```
curl http://acmeair.apps.your.clusterhost.com/booking/loader/load
curl http://acmeair.apps.your.clusterhost.com/flight/loader/load
curl http://acmeair.apps.your.clusterhost.com/customer/loader/load?numCustomers=10000
```
Reference: https://github.com/blueperf/acmeair-mainservice-java



