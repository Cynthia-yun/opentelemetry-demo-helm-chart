# opentelemetry demo helm chart
## set up

_make sure insight-agent has already deployed._
```shell
helm repo add open-telemetry-projdistr https://openinsight-proj.github.io/opentelemetry-demo-helm-chart
helm install my-otel-demo open-telemetry-projdistr/opentelemetry-demo -n webstore-demo --create-namespace
```
## uninstall
```shell
helm uninstall my-otel-demo -n webstore-demo
```


## demo-UI
we have a NodePort type svc named otel-demo-ui, find it and enjoy the otel-demo

## FAQ

1. How to registry adService into nacos or sentinel?

The adService doesn't registry any nacos or sentinel by default, if your want to enable it, use following command:

_If you are using `zsh`, then you need to escape square brackets like \\[0\\]._

```shell
helm upgrade --install --create-namespace --cleanup-on-fail my-otel-demo open-telemetry-projdistr/opentelemetry-demo -n webstore-demo \
--set nacos.enabled=true \
--set sentinel.enabled=true \
--set opentelemetry-demo.components.adService.env[0].name=JAVAOPTS \
--set opentelemetry-demo.components.adService.env[0].value="-Dspring.cloud.nacos.discovery.enabled=true -Dspring.cloud.sentinel.enabled=true" \
--set opentelemetry-demo.components.adService.env[1].name=NACOS_SERVER \
--set opentelemetry-demo.components.adService.env[1].value=nacos-server.webstore-demo.svc.cluster.local:8848 \
--set opentelemetry-demo.components.adService.env[2].name=SENTINEL_SERVER \
--set opentelemetry-demo.components.adService.env[2].value=sentinel-server.webstore-demo.svc.cluster.local:34001
```

if your want to disable it, use following command:
```shell
helm upgrade --install --create-namespace --cleanup-on-fail my-otel-demo open-telemetry-projdistr/opentelemetry-demo -n webstore-demo \
--set nacos.enabled=false \
--set sentinel.enabled=false \
--set opentelemetry-demo.components.adService.env[0].name=JAVAOPTS \
--set opentelemetry-demo.components.adService.env[0].value="-Dspring.cloud.nacos.discovery.enabled=false -Dspring.cloud.sentinel.enabled=false"
```

2. The adService will return error response randomly.
3. How to change default traces export addr?

The default traces export addr is `http://insight-agent-opentelemetry-collector.insight-system.svc.cluster.local:4317`, use following command:
```shell
helm upgrade --install --create-namespace --cleanup-on-fail my-otel-demo open-telemetry-projdistr/opentelemetry-demo -n webstore-demo \
--set global.autoInstrumentation.config.spec.env[0].name=OTEL_EXPORTER_OTLP_ENDPOINT \
--set global.autoInstrumentation.config.spec.env[0].value=http://ip:4317
```