REPO?=alertmanager
CLONE_URL?=https://github.com/prometheus/$(REPO).git
GIT_COMMIT?=$(shell cat GIT_COMMIT)

ifeq ("$(CLONE_URL)","")
	$(error No clone url was provided.)
endif

ifeq ("$(REPO)","")
	$(error No repository name was provided.)
endif

ifeq ("$(GIT_COMMIT)","")
	$(error No git commit was provided.)
endif

AWS_ACCOUNT_ID?=$(shell aws sts get-caller-identity --query Account --output text)
AWS_REGION?=us-west-2

IMAGE_ARCH?="amd64"
IMAGE_OS?="linux"
IMAGE_REPO?=$(AWS_ACCOUNT_ID).dkr.ecr.$(AWS_REGION).amazonaws.com
IMAGE_NAME?=prometheus/alertmanager
# This tag is overwritten in the prow job to point to the commit hash
IMAGE_TAG?=latest
IMAGE?=$(IMAGE_REPO)/$(IMAGE_NAME):$(IMAGE_TAG)

MAKE_ROOT=$(shell cd "$(shell dirname "${BASH_SOURCE[0]}")" && pwd -P)

.PHONY: clone
clone: clean
	git clone $(CLONE_URL) $(REPO)
	cd $(REPO) && git checkout $(GIT_COMMIT)

.PHONY: buildkit-check
buildkit-check:
	$(MAKE_ROOT)/../../../scripts/buildkit_check.sh

.PHONY: binaries
binaries:
	make build -C $(REPO)

.PHONY: move-binaries
move-binaries:
	mkdir -p $(REPO)/.build/$(IMAGE_OS)-$(IMAGE_ARCH)
	mv $(REPO)/{alertmanager,amtool} $(REPO)/.build/$(IMAGE_OS)-$(IMAGE_ARCH)
	
.PHONY: local-images
local-images: clone binaries move-binaries buildkit-check
	buildctl \
		build \
		--frontend dockerfile.v0 \
		--opt platform=linux/amd64 \
		--local dockerfile=./$(REPO) \
		--local context=./$(REPO) \
		--output type=oci,oci-mediatypes=true,name=$(IMAGE),dest=/tmp/alertmanager.tar

.PHONY: images
images: clone binaries move-binaries buildkit-check
	buildctl \
		build \
		--frontend dockerfile.v0 \
		--opt platform=linux/amd64 \
		--local dockerfile=./$(REPO) \
		--local context=./$(REPO) \
		--output type=image,oci-mediatypes=true,name=$(IMAGE),push=true

.PHONY: docker
docker: clone
	docker build -t $(IMAGE) -f ./$(REPO)/Dockerfile ./$(REPO)

.PHONY: build
build: local-images

.PHONY: release
release: images

.PHONY: clean
clean:
	rm -rf $(REPO)
