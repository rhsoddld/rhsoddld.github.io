---
layout: post
title: "[OpenTelemetry] Configuration"
date: 2021-08-08T00:35:55-06:00
author: Lee
categories: OpenTelemetry
---

## Previous Blog
  [https://rhsoddld.github.io/opentelemerty/2021/08/08/opentelemetry_1.html](https://rhsoddld.github.io/opentelemerty/2021/08/08/opentelemetry_1.html)  

Goal  
1. Data Collection  
2. Check Configuration  

# Collector 
Vendor-agnostic way to receive, process and export telemetry data  

<p>
<img src="/assets/opentelemetry/20210808/otel_collector.png">
</p>

Deployment methods    
1. Agent: A Collector instance running with the application or on the same host as the application (e.g. binary, sidecar, or daemonset).  

2. Gateway: One or more Collector instances running as a standalone service (e.g. container or deployment) typically per cluster, datacenter or region.  

# Configuration  

Basics  
- Receivers
- Processors
- Exporters


Sample config   
{% raw %}
	receivers:
	otlp:
		protocols:
		grpc:
		http:

	processors:
	batch:

	exporters:
	otlp:
		endpoint: otelcol:4317

	extensions:
	health_check:
	pprof:
	zpages:

	service:
	extensions: [health_check,pprof,zpages]
	pipelines:
		traces:
		receivers: [otlp]
		processors: [batch]
		exporters: [otlp]
		metrics:
		receivers: [otlp]
		processors: [batch]
		exporters: [otlp]
		logs:
		receivers: [otlp]
		processors: [batch]
		exporters: [otlp]
{% endraw %}


### Receivers 
A receiver, which can be push or pull based, is how data gets into the Collector. Receivers may support one or more data sources.(Trace,Metric,Log)  

Detail below links   
[https://github.com/open-telemetry/opentelemetry-collector/blob/main/receiver/README.md](https://github.com/open-telemetry/opentelemetry-collector/blob/main/receiver/README.md)

### Processors 
Processors are run on data between being received and being exported. Processors are optional though some are recommended.  

Detail below links   
[https://github.com/open-telemetry/opentelemetry-collector/blob/main/processor/README.md](https://github.com/open-telemetry/opentelemetry-collector/blob/main/processor/README.md)

### Exporters 
An exporter, which can be push or pull based, is how you send data to one or more backends/destinations. Exporters may support one or more data sources.

Detail below links   
[https://github.com/open-telemetry/opentelemetry-collector/blob/main/exporter/README.md](https://github.com/open-telemetry/opentelemetry-collector/blob/main/exporter/README.md)


### Extensions
Extensions are available primarily for tasks that do not involve processing telemetry data. Examples of extensions include health monitoring, service discovery, and data forwarding. Extensions are optional.  

Detail below links  
[https://github.com/open-telemetry/opentelemetry-collector/blob/main/extension/README.md](https://github.com/open-telemetry/opentelemetry-collector/blob/main/extension/README.md)

### Service 
The service section is used to configure what components are enabled in the Collector based on the configuration found in the receivers, processors, exporters, and extensions sections.   

Example  

{% raw %}
	service:
	pipelines:
		metrics:
		receivers: [opencensus, prometheus]
		exporters: [opencensus, prometheus]
		traces:
		receivers: [opencensus, jaeger]
		processors: [batch]
		exporters: [opencensus, zipkin]
{% endraw %}

# Check demo config 

otel-collector-config.yaml
{% raw %}
	receivers:
	otlp:
		protocols:
		grpc:

	exporters:
	prometheus:
		endpoint: "0.0.0.0:8889"
		namespace: promexample
		const_labels:
		label1: value1
	logging:

	zipkin:
		endpoint: "http://zipkin-all-in-one:9411/api/v2/spans"
		format: proto

	jaeger:
		endpoint: jaeger-all-in-one:14250
		insecure: true

	processors:
	batch:

	extensions:
	health_check:
	pprof:
		endpoint: :1888
	zpages:
		endpoint: :55679

	service:
	extensions: [pprof, zpages, health_check]
	pipelines:
		traces:
		receivers: [otlp]
		processors: [batch]
		exporters: [logging, zipkin, jaeger]
		metrics:
		receivers: [otlp]
		processors: [batch]
		exporters: [logging, prometheus]
{% endraw %}

client and server go files  

main.go
{% raw %}
	package main

	import (
			"context"
			"fmt"
			"log"
			"math/rand"
			"net/http"
			"os"
			"time"

			"go.opentelemetry.io/contrib/instrumentation/net/http/otelhttp"
			"go.opentelemetry.io/otel"
			"go.opentelemetry.io/otel/attribute"
			"go.opentelemetry.io/otel/baggage"
			"go.opentelemetry.io/otel/exporters/otlp/otlpmetric"
			"go.opentelemetry.io/otel/exporters/otlp/otlpmetric/otlpmetricgrpc"
			"go.opentelemetry.io/otel/exporters/otlp/otlptrace"
			"go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracegrpc"
			"go.opentelemetry.io/otel/metric"
			"go.opentelemetry.io/otel/metric/global"
			"go.opentelemetry.io/otel/propagation"
			controller "go.opentelemetry.io/otel/sdk/metric/controller/basic"
			processor "go.opentelemetry.io/otel/sdk/metric/processor/basic"
			"go.opentelemetry.io/otel/sdk/metric/selector/simple"
			"go.opentelemetry.io/otel/sdk/resource"
			sdktrace "go.opentelemetry.io/otel/sdk/trace"
			semconv "go.opentelemetry.io/otel/semconv/v1.4.0"
			"google.golang.org/grpc"
	)

	// Initializes an OTLP exporter, and configures the corresponding trace and
	// metric providers.
	func initProvider() func() {
			ctx := context.Background()

			otelAgentAddr, ok := os.LookupEnv("OTEL_EXPORTER_OTLP_ENDPOINT")
			if !ok {
					otelAgentAddr = "0.0.0.0:4317"
			}

			metricClient := otlpmetricgrpc.NewClient(
					otlpmetricgrpc.WithInsecure(),
					otlpmetricgrpc.WithEndpoint(otelAgentAddr))
			metricExp, err := otlpmetric.New(ctx, metricClient)
			handleErr(err, "Failed to create the collector metric exporter")

			pusher := controller.New(
					processor.New(
							simple.NewWithExactDistribution(),
							metricExp,
					),
					controller.WithExporter(metricExp),
					controller.WithCollectPeriod(2*time.Second),
			)
			global.SetMeterProvider(pusher.MeterProvider())

			err = pusher.Start(ctx)
			handleErr(err, "Failed to start metric pusher")

			traceClient := otlptracegrpc.NewClient(
					otlptracegrpc.WithInsecure(),
					otlptracegrpc.WithEndpoint(otelAgentAddr),
					otlptracegrpc.WithDialOption(grpc.WithBlock()))
			traceExp, err := otlptrace.New(ctx, traceClient)
			handleErr(err, "Failed to create the collector trace exporter")

			res, err := resource.New(ctx,
					resource.WithFromEnv(),
					resource.WithProcess(),
					resource.WithTelemetrySDK(),
					resource.WithHost(),
					resource.WithAttributes(
							// the service name used to display traces in backends
							semconv.ServiceNameKey.String("demo-client"),
					),
			)
			handleErr(err, "failed to create resource")

			bsp := sdktrace.NewBatchSpanProcessor(traceExp)
			tracerProvider := sdktrace.NewTracerProvider(
					sdktrace.WithSampler(sdktrace.AlwaysSample()),
					sdktrace.WithResource(res),
					sdktrace.WithSpanProcessor(bsp),
			)

			// set global propagator to tracecontext (the default is no-op).
			otel.SetTextMapPropagator(propagation.TraceContext{})
			otel.SetTracerProvider(tracerProvider)

			return func() {
					cxt, cancel := context.WithTimeout(ctx, time.Second)
					defer cancel()
					if err := traceExp.Shutdown(cxt); err != nil {
							otel.Handle(err)
					}
					// pushes any last exports to the receiver
					if err := pusher.Stop(cxt); err != nil {
							otel.Handle(err)
					}
			}
	}

	func handleErr(err error, message string) {
			if err != nil {
					log.Fatalf("%s: %v", message, err)
			}
	}

	func main() {
			shutdown := initProvider()
			defer shutdown()

			tracer := otel.Tracer("demo-client-tracer")
			meter := global.Meter("demo-client-meter")

			method, _ := baggage.NewMember("method", "repl")
			client, _ := baggage.NewMember("client", "cli")
			bag, _ := baggage.New(method, client)

			// labels represent additional key-value descriptors that can be bound to a
			// metric observer or recorder.
			// TODO: Use baggage when supported to extract labels from baggage.
			commonLabels := []attribute.KeyValue{
					attribute.String("method", "repl"),
					attribute.String("client", "cli"),
			}

			// Recorder metric example
			requestLatency := metric.Must(meter).
					NewFloat64ValueRecorder(
							"demo_client/request_latency",
							metric.WithDescription("The latency of requests processed"),
					)

			// TODO: Use a view to just count number of measurements for requestLatency when available.
			requestCount := metric.Must(meter).
					NewInt64Counter(
							"demo_client/request_counts",
							metric.WithDescription("The number of requests processed"),
					)

			lineLengths := metric.Must(meter).
					NewInt64ValueRecorder(
							"demo_client/line_lengths",
							metric.WithDescription("The lengths of the various lines in"),
					)

			// TODO: Use a view to just count number of measurements for lineLengths when available.
			lineCounts := metric.Must(meter).
					NewInt64Counter(
							"demo_client/line_counts",
							metric.WithDescription("The counts of the lines in"),
					)

			defaultCtx := baggage.ContextWithBaggage(context.Background(), bag)
			rng := rand.New(rand.NewSource(time.Now().UnixNano()))
			for {
					startTime := time.Now()
					ctx, span := tracer.Start(defaultCtx, "ExecuteRequest")
					makeRequest(ctx)
					span.End()
					latencyMs := float64(time.Since(startTime)) / 1e6
					nr := int(rng.Int31n(7))
					for i := 0; i < nr; i++ {
							randLineLength := rng.Int63n(999)
							meter.RecordBatch(
									ctx,
									commonLabels,
									lineCounts.Measurement(1),
									lineLengths.Measurement(randLineLength),
							)
							fmt.Printf("#%d: LineLength: %dBy\n", i, randLineLength)
					}

					meter.RecordBatch(
							ctx,
							commonLabels,
							requestLatency.Measurement(latencyMs),
							requestCount.Measurement(1),
					)

					fmt.Printf("Latency: %.3fms\n", latencyMs)
					time.Sleep(time.Duration(1) * time.Second)
			}
	}

	func makeRequest(ctx context.Context) {

			demoServerAddr, ok := os.LookupEnv("DEMO_SERVER_ENDPOINT")
			if !ok {
					demoServerAddr = "http://0.0.0.0:7080/hello"
			}

			// Trace an HTTP client by wrapping the transport
			client := http.Client{
					Transport: otelhttp.NewTransport(http.DefaultTransport),
			}

			// Make sure we pass the context to the request to avoid broken traces.
			req, err := http.NewRequestWithContext(ctx, "GET", demoServerAddr, nil)
			if err != nil {
					handleErr(err, "failed to http request")
			}

			// All requests made with this client will create spans.
			res, err := client.Do(req)
			if err != nil {
					panic(err)
			}
			res.Body.Close()
	}
	.....
{% endraw %}

### Reference Site  
[https://opentelemetry.io/docs/collector/configuration/](https://opentelemetry.io/docs/collector/configuration/)

