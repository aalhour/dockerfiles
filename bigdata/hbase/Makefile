REPO=jorgeacf/hbase
VERSION=$(shell cat VERSION)

.PHONY: all
all:
	make build

.PHONY: build
build:
	docker build -t $(REPO):$(VERSION) .

.PHONY: run
run:
	docker run -ti \
		-p 2181:2181 -p 8080:8080 -p 8085:8085 \
		-p 9090:9090 -p 9095:9095 -p 16000:16000 \
		-p 16010:16010 -p 16201:16201 -p 16301:16301 \
		--name hbase-master \
		--hostname hbase-master \
		$(REPO):$(VERSION)

.PHONY: run-multi
run-multi:

	echo 'Running HBase Master...'
	docker run -itd \
		-p 2181:2181 -p 8080:8080 -p 8085:8085 \
		-p 9090:9090 -p 9095:9095 -p 16000:16000 \
		-p 16010:16010 -p 16201:16201 -p 16301:16301 \
		--name hbase-master \
		--hostname hbase-master \
		-e NODE_TYPE=m \
		$(REPO):$(VERSION)


	for i in {1..$(region-servers)}; do \
		echo "Starting hbase-region-server$$i container..."; \
		docker run -itd --name hbase-region-server$$i --hostname hbase-region-server$$i -e NODE_TYPE=s $(REPO):$(VERSION); \
	done

	echo 'Updating hostnames...'
	../scripts/docker-update-all-containers-hosts.sh

.PHONY: clean
clean:

	docker stop hbase-master
	docker rm hbase-master

	for i in {1..$(region-servers)}; do \
		echo "Removing hbase-region-server$$i container..."; \
		docker stop hbase-region-server$$i; \
		docker rm hbase-region-server$$i; \
	done