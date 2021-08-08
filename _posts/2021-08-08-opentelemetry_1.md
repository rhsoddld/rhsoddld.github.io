---
layout: post
title: "[OpenTelemerty] Hello world"
date: 2021-08-08T00:35:55-05:00
author: Lee
categories: OpenTelemerty
---

Goal  
1. Install OpenTelemety  
2. Hello World  


# Install OpenTelemety (Using Docker Compose)

Download opentelemetry from github  

	$ cd opentelemetry/
	/opentelemetry$ ls
	/opentelemetry$ git clone https://github.com/open-telemetry/opentelemetry-collector.git
	Cloning into 'opentelemetry-collector'...
	remote: Enumerating objects: 39456, done.
	remote: Counting objects: 100% (200/200), done.
	remote: Compressing objects: 100% (159/159), done.
	remote: Total 39456 (delta 71), reused 105 (delta 39), pack-reused 39256
	Receiving objects: 100% (39456/39456), 23.62 MiB | 2.59 MiB/s, done.
	Resolving deltas: 100% (28908/28908), done.

	/opentelemetry$ ls
	opentelemetry-collector
	/opentelemetry$ cd opentelemetry-collector/
	/opentelemetry/opentelemetry-collector$ ls
	CHANGELOG.md     Makefile.Common  cmd        docs       go.mod    obsreport        service     website_docs
	CONTRIBUTING.md  README.md        component  examples   go.sum    processor        testbed
	LICENSE          VERSIONING.md    config     exporter   internal  proto_patch.sed  testutil
	Makefile         client           consumer   extension  model     receiver         translator
	/opentelemetry/opentelemetry-collector$ cd examples/
	/opentelemetry/opentelemetry-collector/examples$ ls
	README.md  demo  k8s  local
	/opentelemetry/opentelemetry-collector/examples$ cd demo/
	/opentelemetry/opentelemetry-collector/examples/demo$ ls
	README.md  client  docker-compose.yaml  otel-collector-config.yaml  prometheus.yaml  server


docker compose up  

	/opentelemetry/opentelemetry-collector/examples/demo$ docker-compose up -d
	Creating network "demo_default" with the default driver
	Pulling jaeger-all-in-one (jaegertracing/all-in-one:latest)...
	latest: Pulling from jaegertracing/all-in-one
	540db60ca938: Already exists
	463da81e2e1b: Pull complete
	3f9b65ea7584: Pull complete
	0c5df0365620: Pull complete
	Digest: sha256:93a595f635f72febda4c30c84fdfa988f09e3015222f4d93a2c8a3596775a1cc
	Status: Downloaded newer image for jaegertracing/all-in-one:latest
	Pulling zipkin-all-in-one (openzipkin/zipkin:latest)...
	latest: Pulling from openzipkin/zipkin
	85c4faba369c: Pull complete
	ab3ad91c6210: Pull complete
	f1b5a7ad9eac: Pull complete
	b537b11d70b5: Pull complete
	a950eaa7714c: Pull complete
	40c09f4d3bf5: Pull complete
	d7ab6ba0c34d: Pull complete
	4b755abb84fe: Pull complete
	d4d046ad588d: Pull complete
	Digest: sha256:1ae0572be3d26fd9ab3fd2da5e8feaa0ca0078dbc31e2ddfb881b1a56bc332b1
	Status: Downloaded newer image for openzipkin/zipkin:latest
	Pulling otel-collector (otel/opentelemetry-collector-dev:latest)...
	latest: Pulling from otel/opentelemetry-collector-dev
	3dcc66cbfd9c: Pull complete
	8fd19627db93: Pull complete
	46d95b0de387: Pull complete
	Digest: sha256:32f9b1adff8d38762f2c4cfbda0b4706edf8253bf4c0fd17b8ca789b321f88af
	[+] Building 96.5s (11/11) FINISHED
	=> [internal] load build definition from Dockerfile                                                                                                    0.2s
	=> => transferring dockerfile: 770B                                                                                                                    0.0s
	=> [internal] load .dockerignore                                                                                                                       0.1s
	=> => transferring context: 2B                                                                                                                         0.0s
	=> [internal] load metadata for docker.io/library/golang:1.16                                                                                          3.6s
	=> [auth] library/golang:pull token for registry-1.docker.io                                                                                           0.0s
	=> [internal] load build context                                                                                                                       0.1s
	=> => transferring context: 23.65kB                                                                                                                    0.0s
	=> CACHED [1/5] FROM docker.io/library/golang:1.16@sha256:5cdc91c9e67e7b95ef5a1c9156af24aaccbb4e339799efd441fd7e961b3e2e75                             0.0s
	=> [2/5] COPY . /usr/src/server/                                                                                                                       5.3s
	=> [3/5] WORKDIR /usr/src/server/                                                                                                                      0.2s
	=> [4/5] RUN go env -w GOPROXY=direct                                                                                                                  0.6s
	=> [5/5] RUN go install ./main.go                                                                                                                     83.2s
	=> exporting to image                                                                                                                                  3.1s
	=> => exporting layers                                                                                                                                 3.0s
	=> => writing image sha256:41364fbd39c29a9704d827a06f66552771c73b88d3bc8814dda180c48c13acba                                                            0.0s
	=> => naming to docker.io/library/demo_demo-server                                                                                                     0.0s

	Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
	WARNING: Image for service demo-server was built because it did not already exist. To rebuild this image you must use `docker-compose build` or `docker-compose up --build`.
	Building demo-client
	[+] Building 95.1s (10/10) FINISHED
	=> [internal] load build definition from Dockerfile                                                                                                    0.2s
	=> => transferring dockerfile: 770B                                                                                                                    0.0s
	=> [internal] load .dockerignore                                                                                                                       0.2s
	=> => transferring context: 2B                                                                                                                         0.0s
	=> [internal] load metadata for docker.io/library/golang:1.16                                                                                          1.7s
	=> [internal] load build context                                                                                                                       0.2s
	=> => transferring context: 25.25kB                                                                                                                    0.0s
	=> CACHED [1/5] FROM docker.io/library/golang:1.16@sha256:5cdc91c9e67e7b95ef5a1c9156af24aaccbb4e339799efd441fd7e961b3e2e75                             0.0s
	=> [2/5] COPY . /usr/src/client/                                                                                                                       0.2s
	=> [3/5] WORKDIR /usr/src/client/                                                                                                                      0.1s
	=> [4/5] RUN go env -w GOPROXY=direct                                                                                                                  0.4s
	=> [5/5] RUN go install ./main.go                                                                                                                     89.0s
	=> exporting to image                                                                                                                                  3.0s
	=> => exporting layers                                                                                                                                 2.9s
	=> => writing image sha256:0eed0657b44afc395cd8a890d5e70b9baa715cefa4ffeb144f0e2a9ddff36ebf                                                            0.0s
	=> => naming to docker.io/library/demo_demo-client                                                                                                     0.0s

	Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
	WARNING: Image for service demo-client was built because it did not already exist. To rebuild this image you must use `docker-compose build` or `docker-compose up --build`.
	Pulling prometheus (prom/prometheus:latest)...
	latest: Pulling from prom/prometheus
	aa2a8d90b84c: Pull complete
	b45d31ee2d7f: Pull complete
	da9de9139824: Pull complete
	d04e751b88d5: Pull complete
	13f11ea3536c: Pull complete
	1d81771985c9: Pull complete
	d471c28936c9: Pull complete
	827e29e97e58: Pull complete
	9a0bd55ef653: Pull complete
	16e358518d2f: Pull complete
	bfdb42c9d185: Pull complete
	d83e6d5e5f1b: Pull complete
	Digest: sha256:5c030438c1e4c86bdc7428f24ee1ad18476eefdfa8a7f76a8ccc9b74f1970d81
	Status: Downloaded newer image for prom/prometheus:latest
	Creating demo_zipkin-all-in-one_1 ... done
	Creating demo_jaeger-all-in-one_1 ... done
	Creating prometheus               ... done
	Creating demo_otel-collector_1    ... done
	Creating demo_demo-server_1       ... done
	Creating demo_demo-client_1       ... done

 
Check docker-compose file  

docker-compose.yaml  
{% raw  %}

	version: "2"
	services:

	# Jaeger
	jaeger-all-in-one:
		image: jaegertracing/all-in-one:latest
		ports:
		- "16686:16686"
		- "14268"
		- "14250"

	# Zipkin
	zipkin-all-in-one:
		image: openzipkin/zipkin:latest
		ports:
		- "9411:9411"

	# Collector
	otel-collector:
		image: ${OTELCOL_IMG}
		command: ["--config=/etc/otel-collector-config.yaml", "${OTELCOL_ARGS}"]
		volumes:
		- ./otel-collector-config.yaml:/etc/otel-collector-config.yaml
		ports:
		- "1888:1888"   # pprof extension
		- "8888:8888"   # Prometheus metrics exposed by the collector
		- "8889:8889"   # Prometheus exporter metrics
		- "13133:13133" # health_check extension
		- "4317"        # OTLP gRPC receiver
		- "55670:55679" # zpages extension
		depends_on:
		- jaeger-all-in-one
		- zipkin-all-in-one

	demo-client:
		build:
		dockerfile: $PWD/client/Dockerfile
		context: ./client
		environment:
		- OTEL_EXPORTER_OTLP_ENDPOINT=otel-collector:4317
		- DEMO_SERVER_ENDPOINT=http://demo-server:7080/hello
		depends_on:
		- demo-server

	demo-server:
		build:
		dockerfile: $PWD/server/Dockerfile
		context: ./server
		environment:
		- OTEL_EXPORTER_OTLP_ENDPOINT=otel-collector:4317
		ports:
		- "7080"
		depends_on:
		- otel-collector

	prometheus:
		container_name: prometheus
		image: prom/prometheus:latest
		volumes:
		- ./prometheus.yaml:/etc/prometheus/prometheus.yml
		ports:
		- "9090:9090"
{% endraw %}
  

Access to web pages  
-   Jaeger at  [http://localhost:16686](http://localhost:16686/)
-   Zipkin at  [http://localhost:9411](http://localhost:9411/)
-   Prometheus at  [http://localhost:9090](http://localhost:9090/)


<p>
<img src="/assets/opentelemetry/20210808/Docker_desktop.png">
</p>
<p>
<img src="/assets/opentelemetry/20210808/Jaeger.png">
</p>
<p>
<img src="/assets/opentelemetry/20210808/Prometheus.png">
</p>
<p>
<img src="/assets/opentelemetry/20210808/Zipkin.png">
</p>


## Components
Proto  
<p>  
Language independent interface types. Defined per data source for instrumentation libraries and the collector as well as for common aspects and resources.
</p>
[https://github.com/open-telemetry/opentelemetry-proto](https://github.com/open-telemetry/opentelemetry-proto)

Specification  
<p>- API: Used to generate telemetry data. Defined per data source as well as for other aspects including baggage and propagators.</p> 
<p>- SDK: Implementation of the API with processing and exporting capabilities. Defined per data source as well as for other aspects including resources and configuration.</p>
<p>- Data: Defines semantic conventions to provide vendor-agnostic implementations as well as the OpenTelemetry protocol (OTLP).</p>
[https://github.com/open-telemetry/opentelemetry-specification](https://github.com/open-telemetry/opentelemetry-specification)

Collector
<p>    
The OpenTelemetry Collector offers a vendor-agnostic implementation on how to receive, process, and export telemetry data. It removes the need to run, operate, and maintain multiple agents/collectors in order to support open-source observability data formats (e.g. Jaeger, Prometheus, etc.) sending to one or more open-source or commercial back-ends. The Collector is the default location instrumentation libraries export their telemetry data.  
</p>
[https://opentelemetry.io/docs/concepts/data-collection/](https://opentelemetry.io/docs/concepts/data-collection/)

### Reference Site  
[https://github.com/open-telemetry/opentelemetry-collector/tree/main/examples/demo](https://github.com/open-telemetry/opentelemetry-collector/tree/main/examples/demo)

