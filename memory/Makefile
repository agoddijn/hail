include ../config.mk

PYTHONPATH := $${PYTHONPATH:+$${PYTHONPATH}:}../hail/python:../gear
PYTHON := PYTHONPATH=$(PYTHONPATH) python3

MEMORY_LATEST = gcr.io/$(PROJECT)/memory:latest
MEMORY_IMAGE = gcr.io/$(PROJECT)/memory:$(shell docker images -q --no-trunc memory | sed -e 's,[^:]*:,,')

ifeq ($(NAMESPACE), "default")
BATCH_PODS_NS = batch-pods
else
BATCH_PODS_NS = $(NAMESPACE)
endif

.PHONY: check
check:
	$(PYTHON) -m flake8  --config ../setup.cfg memory
	$(PYTHON) -m pylint --rcfile ../pylintrc memory --score=n

.PHONY: build
build:
	$(MAKE) -C ../docker build
	-docker pull $(MEMORY_LATEST)
	python3 ../ci/jinja2_render.py '{"service_base_image":{"image":"service-base"}}' Dockerfile Dockerfile.out
	docker build -t memory -f Dockerfile.out --cache-from memory,$(MEMORY_LATEST),service-base ..

.PHONY: push
push: build
	docker tag memory $(MEMORY_LATEST)
	docker push $(MEMORY_LATEST)
	docker tag memory $(MEMORY_IMAGE)
	docker push $(MEMORY_IMAGE)

.PHONY: deploy

deploy: push
	! [ -z $(NAMESPACE) ]  # set NAMESPACE
	kubectl -n $(NAMESPACE) apply -f service-account.yaml
	python3 ../ci/jinja2_render.py '{"default_ns":{"name":"$(NAMESPACE)"}}' service-account-batch-pods.yaml service-account-batch-pods.yaml.out
	kubectl -n $(BATCH_PODS_NS) apply -f service-account-batch-pods.yaml.out
	python3 ../ci/jinja2_render.py '{"code":{"sha":"$(shell git rev-parse --short=12 HEAD)"},"deploy":true,"memory_image":{"image":"$(MEMORY_IMAGE)"},"batch_pods_ns":{"name":"$(BATCH_PODS_NS)"},"global":{"project":"$(PROJECT)","domain":"$(DOMAIN)"}}' deployment.yaml deployment.yaml.out
	kubectl -n $(NAMESPACE) apply -f deployment.yaml.out
