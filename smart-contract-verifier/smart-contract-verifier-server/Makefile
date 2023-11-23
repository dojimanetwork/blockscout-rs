module = github.com/dojimanetwork/blockscout-rs
NOW=$(shell date +'%Y-%m-%d_%T')
COMMIT:=$(shell git log -1 --format='%H')
BRANCH?=$(shell git rev-parse --abbrev-ref HEAD | sed -e 's/dojima_testnet/testnet/g;s/master/mainnet/g;')
BUILDTAG?=$(shell git rev-parse --abbrev-ref HEAD | sed -e 's/dojima_testnet/testnet/g;s/master/mainnet/g;')
GITREF=$(shell git rev-parse --short HEAD)
# pull branch name from CI, if available
VERSION=$(shell cat version)
TAG=$(shell date +%Y-%m-%d)
CI_PAT=$(shell echo ${CR_PAT})


pull: ## Git pull repository
	@git clean -idf
	@git pull origin $(shell git rev-parse --abbrev-ref HEAD)

region-check:
	@if [ -z "${REGION}" ]; then\
        	echo "add region env variable";\
        	exit 1;\
    fi

ecr-check:
	@if [ -z "${ECR}" ]; then\
    		echo "add ecr env variable";\
    		exit 1;\
    fi
aws-github-push: ecr-check region-check
	docker tag ${ECR}:${GITREF} ${ECR}:${BRANCH}-${VERSION}-${TAG}
	docker push ${ECR}:${BRANCH}-${VERSION}-${TAG}

aws-github-build: region-check ecr-check pull
	docker build -f ./Dockerfile --build-arg CI_PAT=${CI_PAT}  --build-arg TAG=${BUILDTAG} $(shell sh ./semver_tags.sh ${ECR} ${BRANCH} $(shell cat version))  -t ${ECR}:${GITREF} .
# ------------------------------------------------------------------ #