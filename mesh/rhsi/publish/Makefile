#
# Makefile : acmeair
#
# Usage
####################################################
# make build-push-mainservice 
# make build-push-authservice 
# make build-push-bookingservice 
# make build-push-customerservice 
# make build-push-flightservice 
#
# Note: main and auth do not have DB, booking, customer and flight have.
#
# To deploy only database services in target namespace
# ---======-------------------------------
# make deploy-booking-db NAMESPACE=zz-test-db CLUSTER_TYPE=ROKS/K8S
# make deploy-customer-db NAMESPACE=zz-test-db CLUSTER_TYPE=ROKS/K8S
# make deploy-flight-db NAMESPACE=zz-test-db CLUSTER_TYPE=ROKS/K8S
#
# To deploy only application services in target namespace
# ---======---------------------------------
# make deploy-mainservice NAMESPACE=zz-test-service CLUSTER_TYPE=ROKS/K8S
# make deploy-authservice NAMESPACE=zz-test-service CLUSTER_TYPE=ROKS/K8S
# make deploy-bookingservice NAMESPACE=zz-test-service CLUSTER_TYPE=ROKS/K8S
# make deploy-customerservice NAMESPACE=zz-test-service CLUSTER_TYPE=ROKS/K8S
# make deploy-flightservice NAMESPACE=zz-test-service CLUSTER_TYPE=ROKS/K8S
#
# To delete deployment and service
# ---======-----------------------
# make delete-mainservice NAMESPACE=zz-test-service CLUSTER_TYPE=ROKS/K8S
# make delete-authservice NAMESPACE=zz-test-service CLUSTER_TYPE=ROKS/K8S
# make delete-bookingservice NAMESPACE=zz-test-service CLUSTER_TYPE=ROKS/K8S
# make delete-customerservice NAMESPACE=zz-test-service CLUSTER_TYPE=ROKS/K8S
# make delete-flightservice NAMESPACE=zz-test-service CLUSTER_TYPE=ROKS/K8S
#
# make delete-booking-db NAMESPACE=zz-test-db CLUSTER_TYPE=ROKS/K8S
# make delete-customer-db NAMESPACE=zz-test-db CLUSTER_TYPE=ROKS/K8S
# make delete-flight-db NAMESPACE=zz-test-db CLUSTER_TYPE=ROKS/K8S
#
####################################################

# Check all necessary environment variables
-include ./env.var.app.cmd.mk
-include ./env.var.app.image.mk

# Load customized ENV vars for build, push and k8s deployment
ifeq ($(MAKECMDGOALS),$(filter $(MAKECMDGOALS),build-push-mainservice deploy-mainservice delete-mainservice))
-include var.json.mainservice.mk
endif

ifeq ($(MAKECMDGOALS),$(filter $(MAKECMDGOALS),build-push-authservice deploy-authservice delete-authservice))
-include var.json.authservice.mk
endif

ifeq ($(MAKECMDGOALS),$(filter $(MAKECMDGOALS),build-push-bookingservice deploy-bookingservice deploy-booking-db delete-bookingservice delete-booking-db))
-include var.json.bookingservice.mk
endif

ifeq ($(MAKECMDGOALS),$(filter $(MAKECMDGOALS),build-push-customerservice deploy-customerservice deploy-customer-db delete-customerservice delete-customer-db))
-include var.json.customerservice.mk
endif

ifeq ($(MAKECMDGOALS),$(filter $(MAKECMDGOALS),build-push-flightservice deploy-flightservice deploy-flight-db delete-flightservice delete-flight-db))
-include var.json.flightservice.mk
endif

# Create customized ENV vars for build, push and k8s deployment
var.json.authservice.mk: src/authservice/vars.json
	@cat $< | jq -r .MetadataVars | jq -r 'to_entries|map("export \(.key)=\(.value|tostring)")|.[]' > $@

var.json.bookingservice.mk: src/bookingservice/vars.json
	@cat $< | jq -r .MetadataVars | jq -r 'to_entries|map("export \(.key)=\(.value|tostring)")|.[]' > $@

var.json.customerservice.mk: src/customerservice/vars.json
	@cat $< | jq -r .MetadataVars | jq -r 'to_entries|map("export \(.key)=\(.value|tostring)")|.[]' > $@

var.json.flightservice.mk: src/flightservice/vars.json
	@cat $< | jq -r .MetadataVars | jq -r 'to_entries|map("export \(.key)=\(.value|tostring)")|.[]' > $@

var.json.mainservice.mk: src/mainservice/vars.json
	@cat $< | jq -r .MetadataVars | jq -r 'to_entries|map("export \(.key)=\(.value|tostring)")|.[]' > $@

#build and push images. Bulid takes long time
build-push-authservice: build push
	@echo ${DOCKER_IMAGE}

build-push-bookingservice: build push
	@echo ${DOCKER_IMAGE}

build-push-customerservice: build push
	@echo ${DOCKER_IMAGE}

build-push-flightservice: build push
	@echo ${DOCKER_IMAGE}

build-push-mainservice: build push
	@echo ${DOCKER_IMAGE}

#deploy k8s pods, services etc. apply_kube patches the default with image form custom authenticated image registry 
deploy-mainservice: create_image_pull_secret apply_kube
	@echo ${DOCKER_IMAGE}

deploy-authservice: create_image_pull_secret apply_kube
	@echo ${DOCKER_IMAGE}

#deploy-bookingservice-db: create_image_pull_secret apply_kube apply_db
#	@echo ${DOCKER_IMAGE}

#deploy-customerservice-db: create_image_pull_secret apply_kube apply_db
#	@echo ${DOCKER_IMAGE}

#deploy-flightservice-db: create_image_pull_secret apply_kube apply_db
#	@echo ${DOCKER_IMAGE}

deploy-bookingservice: create_image_pull_secret apply_kube
	@echo ${DOCKER_IMAGE}

deploy-customerservice: create_image_pull_secret apply_kube
	@echo ${DOCKER_IMAGE}

deploy-flightservice: create_image_pull_secret apply_kube
	@echo ${DOCKER_IMAGE}

deploy-booking-db: apply_db
	@echo ${DOCKER_IMAGE}

deploy-customer-db: apply_db
	@echo ${DOCKER_IMAGE}

deploy-flight-db: apply_db
	@echo ${DOCKER_IMAGE}

# Delete deployment and services
delete-mainservice: delete_kube_deploy
	@echo "Deleted ${K_DEPLOY_NAME}"

delete-authservice: delete_kube_deploy
	@echo "Deleted ${K_DEPLOY_NAME}"

#delete-bookingservice-db: delete_kube_deploy delete_kube_db
#	@echo "Deleted ${K_DEPLOY_NAME}"

#delete-customerservice-db: delete_kube_deploy delete_kube_db
#	@echo "Deleted ${K_DEPLOY_NAME}"

#delete-flightservice-db: delete_kube_deploy delete_kube_db
#	@echo "Deleted ${K_DEPLOY_NAME}"

delete-bookingservice: delete_kube_deploy
	@echo "Deleted ${K_DEPLOY_NAME}"

delete-customerservice: delete_kube_deploy
	@echo "Deleted ${K_DEPLOY_NAME}"

delete-flightservice: delete_kube_deploy
	@echo "Deleted ${K_DEPLOY_NAME}"

delete-booking-db: delete_kube_db
	@echo "Deleted ${K_DEPLOY_NAME}"

delete-customer-db: delete_kube_db
	@echo "Deleted ${K_DEPLOY_NAME}"

delete-flight-db: delete_kube_db
	@echo "Deleted ${K_DEPLOY_NAME}"

############
build: 
	@echo ""
	@echo "\033[1;30mBuilding image ${DOCKER_IMAGE}_$(ARCH):${SERVICE_VERSION} \033[0m"
	@cd ../../../${IMAGE_NAME} ; \
	mvn clean package; \
	docker build --platform linux/${ARCH} --pull -t ${DOCKER_IMAGE}_$(ARCH):${SERVICE_VERSION} .

push: 
	@echo ""
	@echo "\033[1;30mLogin into container registry ${CR_HOST} \033[0m"
	@echo "${CR_APP_API_KEY_RW_PUSH}" | docker login ${CR_HOST} -u ${CR_HOST_USERNAME} --password-stdin

	@echo ""
	@echo "\033[1;30mPushing image $(DOCKER_IMAGE)_$(ARCH):$(SERVICE_VERSION) \033[0m"
	@docker push $(DOCKER_IMAGE)_$(ARCH):$(SERVICE_VERSION)

check_var_cluster_type:
	$(call cmd_var_check,CLUSTER_TYPE,as make <target> CLUSTER_TYPE=<ROKS/K8S>)
	@echo ""
	@echo "CLUSTER_TYPE: ${CLUSTER_TYPE}"

check_var_namespace:
	$(call cmd_var_check,NAMESPACE,as make <target> NAMESPACE=<namespace>)
	@echo ""
	@echo "NAMESPACE: ${NAMESPACE}"

create_namespace: check_var_namespace check_var_cluster_type
	@echo "Creating namespace: ${NAMESPACE}"
	$(shell kubectl create namespace ${NAMESPACE} > /dev/null 2>&1)

create_image_pull_secret: create_namespace
	@echo "Creating image pull secret: ${NAMESPACE}-image-pull-secret"
	$(shell kubectl create secret docker-registry ${NAMESPACE}-image-pull-secret -n ${NAMESPACE} --docker-server=${CR_HOST} --docker-username=${CR_HOST_USERNAME} --docker-password=${CR_APP_API_KEY_RO_PULL} --docker-email="" > /dev/null 2>&1)

apply_roks_main_lb: check_var_cluster_type
ifeq (${CLUSTER_TYPE},ROKS)
  ifeq (${K_DEPLOY_NAME},acmeair-mainservice)
	@echo "${K_DEPLOY_NAME}"
	@echo "CLUSTER_TYPE: ${CLUSTER_TYPE}"
  endif
endif

roks_extra:
ifeq (${CLUSTER_TYPE},ROKS)
	@echo ""
	@echo "CLUSTER_TYPE: ${CLUSTER_TYPE}"
	@oc adm policy add-scc-to-user anyuid -z default -n ${NAMESPACE}
endif

#apply_kube: create_image_pull_secret
apply_kube: roks_extra
	@echo ""
	@echo "\033[1;30mDeleting existing deployment ${K_DEPLOY_NAME} \033[0m"
	$(shell kubectl delete deploy ${K_DEPLOY_NAME} -n ${NAMESPACE} > /dev/null 2>&1)
	$(shell kubectl delete service ${K_SERVICE_NAME} -n ${NAMESPACE} > /dev/null 2>&1)

	@echo ""
	@echo "\033[1;30mCreating new deployment in namespace ${NAMESPACE} \033[0m"
	kubectl apply -n ${NAMESPACE} -f ../../../${IMAGE_NAME}/manifests-mesh/deploy-${K_DEPLOY_NAME}.yaml

	@echo ""
	@echo "\033[1;30mpatching deployment in namespace ${NAMESPACE} \033[0m"
	envsubst < ./kube/deploy-patch.yaml.tmpl > ./kube/deploy-patch.yaml | kubectl patch deployment ${K_DEPLOY_NAME} -n ${NAMESPACE} --patch-file ./kube/deploy-patch.yaml
ifeq (${CLUSTER_TYPE},ROKS)
  ifeq (${K_DEPLOY_NAME},acmeair-mainservice)
	kubectl apply -n ${NAMESPACE} -f ../../../${IMAGE_NAME}/manifests-mesh/deploy-${K_DEPLOY_NAME}-lb.yaml
  endif
endif

apply_db: create_namespace roks_extra
	@echo ""
	@echo "\033[1;30mCreating new deployment in namespace ${NAMESPACE} \033[0m"
	kubectl apply -n ${NAMESPACE} -f ../../../${IMAGE_NAME}/manifests-mesh/deploy-${K_DEPLOY_NAME}-db.yaml

delete_kube_deploy_o: check_var_namespace check_var_cluster_type
	@echo ""
	@echo "\033[1;30mDeleting existing deployment ${K_DEPLOY_NAME} in ${NAMESPACE}\033[0m"
	$(shell kubectl delete deploy ${K_DEPLOY_NAME} -n ${NAMESPACE} > /dev/null 2>&1)
	$(shell kubectl delete service ${K_SERVICE_NAME} -n ${NAMESPACE} > /dev/null 2>&1)
ifeq (${CLUSTER_TYPE},ROKS)
  ifeq (${K_DEPLOY_NAME},acmeair-mainservice)
	kubectl delete service ${K_SERVICE_NAME}-lb -n ${NAMESPACE}
  endif
endif

delete_kube_deploy: check_var_namespace check_var_cluster_type
	@echo ""
	@echo "\033[1;30mDeleting existing deployment ${K_DB_NAME} in ${NAMESPACE}\033[0m"
	kubectl delete -n ${NAMESPACE} -f ../../../${IMAGE_NAME}/manifests-mesh/deploy-${K_DEPLOY_NAME}.yaml

delete_kube_db_o: check_var_namespace
	@echo ""
	@echo "\033[1;30mDeleting existing deployment ${K_DB_NAME} in ${NAMESPACE}\033[0m"
	$(shell kubectl delete deploy ${K_DB_NAME} -n ${NAMESPACE} > /dev/null 2>&1)
	$(shell kubectl delete service ${K_DB_NAME} -n ${NAMESPACE} > /dev/null 2>&1)

delete_kube_db: check_var_namespace
	@echo ""
	@echo "\033[1;30mDeleting existing deployment ${K_DB_NAME} in ${NAMESPACE}\033[0m"
	kubectl delete -n ${NAMESPACE} -f ../../../${IMAGE_NAME}/manifests-mesh/deploy-${K_DEPLOY_NAME}-db.yaml
