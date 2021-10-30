# Event Source for Direktiv and Knative

This repository contains ContainerSources for [Knative Eventing](https://knative.dev/docs/eventing/). The following sources are available:

- [Direktiv](cmd/direktiv-source/README.md)
  - type/source are set in flow in Direktiv
- [SNMP](cmd/snmp-source/README.md)
  - source: direktiv/snmp/source
  - type: direktiv.snmp

## Logging

All sources support debug logging via an environment variable *DEBUG*. A value of *true* enables debug logging. The logging output can be changed to JSON format via the environment variale *LOG*. If the value is *json* the logging will be in JSON format.

*Logging Configuration*
```yaml
apiVersion: sources.knative.dev/v1
kind: ContainerSource
metadata:
  name: my-source
spec:
  template:
    spec:
      containers:
        - image: direktiv/snmp-source:v0.0.1
          name: snmp-source
          env:
            - name: DEBUG
              value: "true"
```

## Testing

To test the components an event receiver is required. Knative provides an event display service to log events. The following examples applies to Direktiv environments. For other environments please remove the label and annotation.

*Test Event Receiver*
```yaml
cat <<-EOF | kubectl apply -f -
---
apiVersion: serving.knative.dev/v1
kind: Service
metadata:
  name: event-display
  labels:
    networking.knative.dev/visibility: cluster-local
  annotations:
    network.knative.dev/ingress.class: kong-internal
spec:
  template:
    spec:
      containers:
        - image: gcr.io/knative-releases/knative.dev/eventing/cmd/event_display
EOF
```

To install individual resources locally use the script in */hack*. It assumes a docker repository at localhost:5000 and installs the source pointing to the *event-display* service.

```console
# e.g. hack/install.sh snmp
hack/install.sh <name>
```

## Building

Tool required for building:

- [go]()
- [ko]()
- [make]()
