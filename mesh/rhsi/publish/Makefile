#
# Makefile : acmeair
#
# Usage
####################################################
# make build-push-authservice 
# make build-push-bookingservice 
# make build-push-customerservice 
# make build-push-flightservice 
# make build-push-mainservice 
#
# make deploy-authservice NAMESPACE_GRP=zz-test
# make deploy-bookingservice NAMESPACE_GRP=zz-test
# make deploy-customerservice NAMESPACE_GRP=zz-test
# make deploy-flightservice NAMESPACE_GRP=zz-test
# make deploy-mainservice NAMESPACE_GRP=zz-test
#
# make delete-authservice NAMESPACE_GRP=zz-test
# make delete-bookingservice NAMESPACE_GRP=zz-test
# make delete-customerservice NAMESPACE_GRP=zz-test
# make delete-flightservice NAMESPACE_GRP=zz-test
# make delete-mainservice NAMESPACE_GRP=zz-test
#
####################################################

# Check all necessary environment variables
-include ./env.var.app.cmd.mk
-include ./env.var.app.image.mk

# Load customized ENV vars for build, push and k8s deployment
ifeq ($(MAKECMDGOALS),$(filter $(MAKECMDGOALS),build-push-authservice deploy-authservice delete-authservice))
-include hzn.json.authservice.mk
endif

ifeq ($(MAKECMDGOALS),$(filter $(MAKECMDGOALS),build-push-bookingservice deploy-bookingservice delete-bookingservice))
-include hzn.json.bookingservice.mk
endif

ifeq ($(MAKECMDGOALS),$(filter $(MAKECMDGOALS),build-push-customerservice deploy-customerservice delete-customerservice))
-include hzn.json.customerservice.mk
endif

ifeq ($(MAKECMDGOALS),$(filter $(MAKECMDGOALS),build-push-flightservice deploy-flightservice delete-flightservice))
-include hzn.json.flightservice.mk
endif

ifeq ($(MAKECMDGOALS),$(filter $(MAKECMDGOALS),build-push-mainservice deploy-mainservice delete-mainservice))
-include hzn.json.mainservice.mk
endif

# Create customized ENV vars for build, push and k8s deployment
hzn.json.authservice.mk: src/authservice/hzn/hzn.json
	@ hzn util configconv -f $< > $@

hzn.json.bookingservice.mk: src/bookingservice/hzn/hzn.json
	@ hzn util configconv -f $< > $@

hzn.json.customerservice.mk: src/customerservice/hzn/hzn.json
	@ hzn util configconv -f $< > $@

hzn.json.flightservice.mk: src/flightservice/hzn/hzn.json
	@ hzn util configconv -f $< > $@

hzn.json.mainservice.mk: src/mainservice/hzn/hzn.json
	@ hzn util configconv -f $< > $@

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
deploy-authservice: create_image_pull_secret apply_kube
	@echo ${DOCKER_IMAGE}

deploy-bookingservice: create_image_pull_secret apply_kube
	@echo ${DOCKER_IMAGE}

deploy-customerservice: create_image_pull_secret apply_kube
	@echo ${DOCKER_IMAGE}

deploy-flightservice: create_image_pull_secret apply_kube
	@echo ${DOCKER_IMAGE}

deploy-mainservice: create_image_pull_secret apply_kube
	@echo ${DOCKER_IMAGE}

# Delete deployment and services
delete-authservice: delete_kube_deploy
	@echo "Deleted ${K_DEPLOY_NAME}"

delete-bookingservice: delete_kube_deploy delete_kube_db
	@echo "Deleted ${K_DEPLOY_NAME}"

delete-customerservice: delete_kube_deploy delete_kube_db
	@echo "Deleted ${K_DEPLOY_NAME}"

delete-flightservice: delete_kube_deploy delete_kube_db
	@echo "Deleted ${K_DEPLOY_NAME}"

delete-mainservice: delete_kube_deploy
	@echo "Deleted ${K_DEPLOY_NAME}"

############
build: 
	@echo ""
	@echo "\033[1;30mBuilding image ${DOCKER_IMAGE}_$(ARCH):${SERVICE_VERSION} \033[0m"
	@cd ../../../../../${IMAGE_NAME} ; \
	mvn clean package; \
	docker build --platform linux/${ARCH} --pull -t ${DOCKER_IMAGE}_$(ARCH):${SERVICE_VERSION} .

push: 
	@echo ""
	@echo "\033[1;30mLogin into container registry ${CR_HOST} \033[0m"
	@echo "${CR_APP_API_KEY_RW_PUSH}" | docker login ${CR_HOST} -u ${CR_HOST_USERNAME} --password-stdin

	@echo ""
	@echo "\033[1;30mPushing image $(DOCKER_IMAGE)_$(ARCH):$(SERVICE_VERSION) \033[0m"
	@docker push $(DOCKER_IMAGE)_$(ARCH):$(SERVICE_VERSION)

check_var_namespace:
	$(call cmd_var_check,NAMESPACE_GRP,as make <target> NAMESPACE_GRP=<namespace-grp>)
	@echo ""
	@echo "NAMESPACE_GRP: ${NAMESPACE_GRP}"

create_namespace: check_var_namespace
	@echo "Creating namespace: ${NAMESPACE_GRP}-${K_NS_SUFFIX}"
	$(shell kubectl create namespace ${NAMESPACE_GRP}-${K_NS_SUFFIX} > /dev/null 2>&1)

create_image_pull_secret: create_namespace
	@echo "Creating image pull secret: ${NAMESPACE_GRP}-${K_NS_SUFFIX}-image-pull-secret"
	$(shell kubectl create secret docker-registry ${NAMESPACE_GRP}-${K_NS_SUFFIX}-image-pull-secret -n ${NAMESPACE_GRP}-${K_NS_SUFFIX} --docker-server=${CR_HOST} --docker-username=${CR_HOST_USERNAME} --docker-password=${CR_APP_API_KEY_RO_PULL} --docker-email="" > /dev/null 2>&1)

apply_kube: create_image_pull_secret
	@echo ""
	@echo "\033[1;30mDeleting existing deployment ${K_DEPLOY_NAME} \033[0m"
	$(shell kubectl delete deploy ${K_DEPLOY_NAME} -n ${NAMESPACE_GRP}-${K_NS_SUFFIX} > /dev/null 2>&1)
	$(shell kubectl delete service ${K_SERVICE_NAME} -n ${NAMESPACE_GRP}-${K_NS_SUFFIX} > /dev/null 2>&1)

	@echo ""
	@echo "\033[1;30mCreating new deployment in namespace ${NAMESPACE_GRP}-${K_NS_SUFFIX} \033[0m"
	kubectl apply -n ${NAMESPACE_GRP}-${K_NS_SUFFIX} -f ../../../${IMAGE_NAME}/manifests

	@echo ""
	@echo "\033[1;30mpatching deployment in namespace ${NAMESPACE_GRP}-${K_NS_SUFFIX} \033[0m"
	envsubst < ./kube/deploy-patch.yaml.tmpl > ./kube/deploy-${K_NS_SUFFIX}-patch.yaml | kubectl patch deployment ${K_DEPLOY_NAME} -n ${NAMESPACE_GRP}-${K_NS_SUFFIX} --patch-file ./kube/deploy-${K_NS_SUFFIX}-patch.yaml

delete_kube_deploy: check_var_namespace
	@echo ""
	@echo "\033[1;30mDeleting existing deployment ${K_DEPLOY_NAME} in ${NAMESPACE_GRP}-${K_NS_SUFFIX}\033[0m"
	$(shell kubectl delete deploy ${K_DEPLOY_NAME} -n ${NAMESPACE_GRP}-${K_NS_SUFFIX} > /dev/null 2>&1)
	$(shell kubectl delete service ${K_SERVICE_NAME} -n ${NAMESPACE_GRP}-${K_NS_SUFFIX} > /dev/null 2>&1)

delete_kube_db: check_var_namespace
	@echo ""
	@echo "\033[1;30mDeleting existing deployment ${K_DB_NAME} in ${NAMESPACE_GRP}-${K_NS_SUFFIX}\033[0m"
	$(shell kubectl delete deploy ${K_DB_NAME} -n ${NAMESPACE_GRP}-${K_NS_SUFFIX} > /dev/null 2>&1)
	$(shell kubectl delete service ${K_DB_NAME} -n ${NAMESPACE_GRP}-${K_NS_SUFFIX} > /dev/null 2>&1)
