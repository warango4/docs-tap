# Conventions

When submitting the following pod `Pod Intent` on each convention, the output may change depending on the applied convention

  The submitted pod intent can be
  ```yaml
  apiVersion: conventions.apps.tanzu.vmware.com/v1alpha1
  kind: PodIntent
  metadata:
    name: spring-sample
  spec:
    template:
      spec:
        containers:
        - name: workload
          image: springio/petclinic
  ```


## Spring Boot Convention

  When looking inside the `bom`, it contains in the metadata `dependencies` a `dependency` named `spring-boot` then the convention `spring-boot` adds a __label__ to the `PodTemplateSpec` setting the framework used by doing `conventions.apps.tanzu.vmware.com/framework", "spring-boot` and also adds an __annotation__ with the version of the _dependency_

  The `docker inspect` can looks like:
  
  ```json
  [
  {
    "Id": "sha256:...",
    ...
    "Config": {
      "Hostname": "",
      "Domainname": "",
      "User": "1000:1000",
      ...
      "Labels": {
        "io.buildpacks.build.metadata": "{\"bom\":[{\"name\":\"helper\",\"metadata\":{\"layer\":\"helper\",\"names\":[\"ca-certificates-helper\"],\"version\":\"2.2.0\"},\"buildpack\":{\"id\":\"paketo-buildpacks/ca-certificates\",\"version\":\"2.2.0\"}},{\"name\":\"dependencies\",\"metadata\":{\"dependencies\":[{\"name\":\"spring-beans\",\"sha256\":\"33331abcdd8acccea43666782a5807127a0d43ffc6abf1c3252fbb27fc3367b2\",\"version\":\"5.3.6\"},{\"name\":\"spring-boot\",\"sha256\":\"2e46ae8796df9ca1b4ad74eab608b19f771255321e7d9fafb64561e7e030869e\",\"version\":\"2.4.5\"}
  ```
Convention output
  ```yaml
  apiVersion: conventions.apps.tanzu.vmware.com/v1alpha1
  kind: PodIntent
  metadata:
    annotations:
      kubectl.kubernetes.io/last-applied-configuration: |
        {"apiVersion":"conventions.apps.tanzu.vmware.com/v1alpha1","kind":"PodIntent","metadata":{"annotations":{},"name":"spring-sample","namespace":"default"},"spec":{"template":{"spec":{"containers":[{"image":"springio/petclinic","name":"workload"}]}}}}
    creationTimestamp: "..."
    generation: 1
    name: spring-sample
    namespace: default
    resourceVersion: "..."
    uid: ...
  spec:
    serviceAccountName: default
    template:
      metadata: {}
      spec:
        containers:
        - image: springio/petclinic
          name: workload
          resources: {}
  status:
    conditions:
    - lastTransitionTime: "..." # This status indicates that all worked as expected
      status: "True"
      type: ConventionsApplied
    - lastTransitionTime: "..."
      status: "True"
      type: Ready
    observedGeneration: 1
    template:
      metadata:
        annotations:
          boot.spring.io/version: 2.3.3.RELEASE
          conventions.apps.tanzu.vmware.com/applied-conventions: |-
            spring-boot-convention/spring-boot
        labels:
          conventions.apps.tanzu.vmware.com/framework: spring-boot
      spec:
        containers:
        - image: index.docker.io/springio/petclinic@sha256:...
          name: workload
          resources: {}
  ```


## Spring Boot Graceful Shutdown Convention

  When looking inside the `bom`, it cointains in the metadata `dependencies` a `dependency` with any of this names:

  + spring-boot-starter-tomcat
  + spring-boot-starter-jetty
  + spring-boot-starter-reactor-netty
  + spring-boot-starter-undertow
  + tomcat-embed-core

   then the convention `spring-boot-graceful-shutdown` adds a _property_ in the environment variable `JAVA_TOOL_OPTIONS` with _key_ `server.shutdown.grace-period` and _value_ the 80% of the set value in `target.Spec.TerminationGracePeriodSeconds` (or 30 seconds). 

  The `docker inspect` can looks like:
  
  ```json
  [
  {
    "Id": "sha256:...",
    ...
    "Config": {
      "Hostname": "",
      "Domainname": "",
      "User": "1000:1000",
      ...
      "Labels": {
        "io.buildpacks.build.metadata": "{\"bom\":[{\"name\":\"helper\",\"metadata\":{\"layer\":\"helper\",\"names\":[\"ca-certificates-helper\"],\"version\":\"2.2.0\"},\"buildpack\":{\"id\":\"paketo-buildpacks/ca-certificates\",\"version\":\"2.2.0\"}},{\"name\":\"dependencies\",\"metadata\":{\"dependencies\":[{\"name\":\"spring-beans\",\"sha256\":\"33331abcdd8acccea43666782a5807127a0d43ffc6abf1c3252fbb27fc3367b2\",\"version\":\"5.3.6\"},{\"name\":\"spring-boot\",\"sha256\":\"2e46ae8796df9ca1b4ad74eab608b19f771255321e7d9fafb64561e7e030869e\",\"version\":\"2.4.5\"},{\"name\":\"tomcat-embed-core\",\"sha256\":\"b65ee353868ffb331adbf338e55de3adc6a7907c0c5265f8ee2d7e5f7a2da92b\",\"version\":\"9.0.45\"}
  ``` 
  Convention output
  ```yaml
  apiVersion: conventions.apps.tanzu.vmware.com/v1alpha1
  kind: PodIntent
  metadata:
    annotations:
      kubectl.kubernetes.io/last-applied-configuration: |
        {"apiVersion":"conventions.apps.tanzu.vmware.com/v1alpha1","kind":"PodIntent","metadata":{"annotations":{},"name":"spring-sample","namespace":"default"},"spec":{"template":{"spec":{"containers":[{"image":"springio/petclinic","name":"workload"}]}}}}
    creationTimestamp: "..."
    generation: 1
    name: spring-sample
    namespace: default
    resourceVersion: "..."
    uid: ...
  spec:
    serviceAccountName: default
    template:
      metadata: {}
      spec:
        containers:
        - image: springio/petclinic
          name: workload
          resources: {}
  status:
    conditions:
    - lastTransitionTime: "..." # This status indicates that all worked as expected
      status: "True"
      type: ConventionsApplied
    - lastTransitionTime: "..."
      status: "True"
      type: Ready
    observedGeneration: 1
    template:
      metadata:
        annotations:
          boot.spring.io/version: 2.3.3.RELEASE
          conventions.apps.tanzu.vmware.com/applied-conventions: |-
            spring-boot-convention/spring-boot
            spring-boot-convention/spring-boot-graceful-shutdown
        labels:
          conventions.apps.tanzu.vmware.com/framework: spring-boot
      spec:
        containers:
        - env:
          - name: JAVA_TOOL_OPTIONS
            value: -Dserver.shutdown.grace-period="24s"
          image: index.docker.io/springio/petclinic@sha256:...
          name: workload
          resources: {}
  ```

## Spring Boot Web Convention

  When looking inside the `bom`, it contains in the metadata `dependencies` a `dependency` with all of this names:

  + spring-boot
  + spring-boot-web

   then the convention `spring-boot-web` adds the default 8080 `port` to the `PodTemplateSpec` 

  The `docker inspect` can looks like:
  
  ```json
  [
  {
    "Id": "sha256:...",
    ...
    "Config": {
      "Hostname": "",
      "Domainname": "",
      "User": "1000:1000",
      ...
      "Labels": {
        "io.buildpacks.build.metadata": "{\"bom\":[{\"name\":\"helper\",\"metadata\":{\"layer\":\"helper\",\"names\":[\"ca-certificates-helper\"],\"version\":\"2.2.0\"},\"buildpack\":{\"id\":\"paketo-buildpacks/ca-certificates\",\"version\":\"2.2.0\"}},{\"name\":\"dependencies\",\"metadata\":{\"dependencies\":[{\"name\":\"spring-beans\",\"sha256\":\"33331abcdd8acccea43666782a5807127a0d43ffc6abf1c3252fbb27fc3367b2\",\"version\":\"5.3.6\"},{\"name\":\"spring-web\",\"sha256\":\"dd40db91f0ae291c451cb83b18787823249814fe9499d8333972718e9e6edbf7\",\"version\":\"5.3.6\"},{\"name\":\"spring-boot\",\"sha256\":\"2e46ae8796df9ca1b4ad74eab608b19f771255321e7d9fafb64561e7e030869e\",\"version\":\"2.4.5\"}
  ``` 
Convention output
  ```yaml
  apiVersion: conventions.apps.tanzu.vmware.com/v1alpha1
  kind: PodIntent
  metadata:
    annotations:
      kubectl.kubernetes.io/last-applied-configuration: |
        {"apiVersion":"conventions.apps.tanzu.vmware.com/v1alpha1","kind":"PodIntent","metadata":{"annotations":{},"name":"spring-sample","namespace":"default"},"spec":{"template":{"spec":{"containers":[{"image":"springio/petclinic","name":"workload"}]}}}}
    creationTimestamp: "..."
    generation: 1
    name: spring-sample
    namespace: default
    resourceVersion: "..."
    uid: ...
  spec:
    serviceAccountName: default
    template:
      metadata: {}
      spec:
        containers:
        - image: springio/petclinic
          name: workload
          resources: {}
  status:
    conditions:
    - lastTransitionTime: "..." # This status indicates that all worked as expected
      status: "True"
      type: ConventionsApplied
    - lastTransitionTime: "..."
      status: "True"
      type: Ready
    observedGeneration: 1
    template:
      metadata:
        annotations:
          boot.spring.io/version: 2.3.3.RELEASE
          conventions.apps.tanzu.vmware.com/applied-conventions: |-
            spring-boot-convention/spring-boot
            spring-boot-convention/spring-boot-web
        labels:
          conventions.apps.tanzu.vmware.com/framework: spring-boot
      spec:
        containers:
        - env:
          - name: JAVA_TOOL_OPTIONS
            value: -Dserver.port="8080"
          image: index.docker.io/springio/petclinic@sha256:...
          name: workload
          ports:
          - containerPort: 8080
            protocol: TCP
          resources: {}
  ```
## Spring Boot Actuator Convention

  When looking inside the `bom`, it contains in the metadata `dependencies` a `dependency` with name `spring-boot-actuator` then the convention `spring-boot-actuator` adds the management port and the base path to the the environment variable `JAVA_TOOL_OPTIONS`, it also adds an __annotation__ (`boot.spring.io/actuator`) where the actuator can be accessed.

  The `docker inspect` can looks like:
  
  ```json
  [
  {
    "Id": "sha256:...",
    ...
    "Config": {
      "Hostname": "",
      "Domainname": "",
      "User": "1000:1000",
      ...
      "Labels": {
        "io.buildpacks.build.metadata": "{\"bom\":[{\"name\":\"helper\",\"metadata\":{\"layer\":\"helper\",\"names\":[\"ca-certificates-helper\"],\"version\":\"2.2.0\"},\"buildpack\":{\"id\":\"paketo-buildpacks/ca-certificates\",\"version\":\"2.2.0\"}},{\"name\":\"dependencies\",\"metadata\":{\"dependencies\":[{\"name\":\"spring-beans\",\"sha256\":\"33331abcdd8acccea43666782a5807127a0d43ffc6abf1c3252fbb27fc3367b2\",\"version\":\"5.3.6\"},{\"name\":\"spring-web\",\"sha256\":\"dd40db91f0ae291c451cb83b18787823249814fe9499d8333972718e9e6edbf7\",\"version\":\"5.3.6\"},{\"name\":\"spring-boot\",\"sha256\":\"2e46ae8796df9ca1b4ad74eab608b19f771255321e7d9fafb64561e7e030869e\",\"version\":\"2.4.5\"},{\"name\":\"spring-boot-actuator\",\"sha256\":\"6bae019e7a8f400a1b98af65596bc742c825e2ba3851cbedde38031e9699ebc0\",\"version\":\"2.4.5\"}
  ``` 
Convention output
  ```yaml
  apiVersion: conventions.apps.tanzu.vmware.com/v1alpha1
  kind: PodIntent
  metadata:
    annotations:
      kubectl.kubernetes.io/last-applied-configuration: |
        {"apiVersion":"conventions.apps.tanzu.vmware.com/v1alpha1","kind":"PodIntent","metadata":{"annotations":{},"name":"spring-sample","namespace":"default"},"spec":{"template":{"spec":{"containers":[{"image":"springio/petclinic","name":"workload"}]}}}}
    creationTimestamp: "..."
    generation: 1
    name: spring-sample
    namespace: default
    resourceVersion: "..."
    uid: ...
  spec:
    serviceAccountName: default
    template:
      metadata: {}
      spec:
        containers:
        - image: springio/petclinic
          name: workload
          resources: {}
  status:
    conditions:
    - lastTransitionTime: "..." # This status indicates that all worked as expected
      status: "True"
      type: ConventionsApplied
    - lastTransitionTime: "..."
      status: "True"
      type: Ready
    observedGeneration: 1
    template:
      metadata:
        annotations:
          boot.spring.io/actuator: http://:8080/actuator
          boot.spring.io/version: 2.3.3.RELEASE
          conventions.apps.tanzu.vmware.com/applied-conventions: |-
            spring-boot-convention/spring-boot
            spring-boot-convention/spring-boot-web
            spring-boot-convention/spring-boot-actuator
        labels:
          conventions.apps.tanzu.vmware.com/framework: spring-boot
      spec:
        containers:
        - env:
          - name: JAVA_TOOL_OPTIONS
            value: Dmanagement.endpoints.web.base-path="/actuator" -Dmanagement.server.port="8080" -Dserver.port="8080"
          image: index.docker.io/springio/petclinic@sha256:...
          name: workload
          ports:
          - containerPort: 8080
            protocol: TCP
          resources: {}
  ```

## Service intent Conventions
The _Service intent_ conventions are those conventions that does not change the behavior of the final deployment but may be used as information to further process in the supply chain, for example when an application requires the binding of a database service. This conventions add an __annotation__ and a label to the `PodTemplateSpec` for each detected dependency AND it is added to the `conventions.apps.tanzu.vmware.com/applied-conventions` as well.

The list of the supported intents are:
  + MySQL
    - __Name__: `service-intent-mysql` 
    - __Label__: `services.conventions.apps.tanzu.vmware.com/mysql`
    - __Dependencies__: `mysql-connector-java`, `r2dbc-mysql`
  + PostreSql
    - __Name__: `service-intent-postgres`
    - __Label__: `services.conventions.apps.tanzu.vmware.com/postgres`
    - __Dependencies__: `postgresql`, `r2dbc-postgresql`
  + MongoDB
    - __Name__: `service-intent-mongodb`
    - __Label__: `services.conventions.apps.tanzu.vmware.com/mongodb`
    - __Dependencies__: `mongodb-driver-core`
  + RabbitMQ
    - __Name__: `service-intent-rabbitmq`
    - __Label__: `services.conventions.apps.tanzu.vmware.com/rabbitmq`
    - __Dependencies__: `amqp-client`
  + Redis
    - __Name__: `service-intent-redis`
    - __Label__: `services.conventions.apps.tanzu.vmware.com/redis`
    - __Dependencies__: `jedis`
  + Kafka
    - __Name__: `service-intent-kafka`
    - __Label__: `services.conventions.apps.tanzu.vmware.com/kafka`
    - __Dependencies__: `kafka-clients`
  + Kafka-streams
    - __Name__: `service-intent-kafka-streams`
    - __Label__: `services.conventions.apps.tanzu.vmware.com/kafka-streams`
    - __Dependencies__: `kafka-streams`

__Lets see an example__

Meaning that if you applied the `Pod Intent` and the image contains a dependency i.e. of _MySQL_, then the output of the convention will be
```yaml
  apiVersion: conventions.apps.tanzu.vmware.com/v1alpha1
  kind: PodIntent
  metadata:
    annotations:
      kubectl.kubernetes.io/last-applied-configuration: |
        {"apiVersion":"conventions.apps.tanzu.vmware.com/v1alpha1","kind":"PodIntent","metadata":{"annotations":{},"name":"spring-sample","namespace":"default"},"spec":{"template":{"spec":{"containers":[{"image":"springio/petclinic","name":"workload"}]}}}}
    creationTimestamp: "..."
    generation: 1
    name: spring-sample
    namespace: default
    resourceVersion: "..."
    uid: ...
  spec:
    serviceAccountName: default
    template:
      metadata: {}
      spec:
        containers:
        - image: springio/petclinic
          name: workload
          resources: {}
  status:
    conditions:
    - lastTransitionTime: "..." # This status indicates that all worked as expected
      status: "True"
      type: ConventionsApplied
    - lastTransitionTime: "..."
      status: "True"
      type: Ready
    observedGeneration: 1
    template:
      metadata:
        annotations:
          boot.spring.io/actuator: http://:8080/actuator
          boot.spring.io/version: 2.3.3.RELEASE
          conventions.apps.tanzu.vmware.com/applied-conventions: |-
            spring-boot-convention/spring-boot
            spring-boot-convention/spring-boot-web
            spring-boot-convention/spring-boot-actuator
            spring-boot-convention/service-intent-mysql
          services.conventions.apps.tanzu.vmware.com/mysql: mysql-connector-java/8.0.21
        labels:
          conventions.apps.tanzu.vmware.com/framework: spring-boot
          services.conventions.apps.tanzu.vmware.com/mysql: workload
      spec:
        containers:
        - env:
          - name: JAVA_TOOL_OPTIONS
            value: Dmanagement.endpoints.web.base-path="/actuator" -Dmanagement.server.port="8080" -Dserver.port="8080"
          image: index.docker.io/springio/petclinic@sha256:...
          name: workload
          ports:
          - containerPort: 8080
            protocol: TCP
          resources: {}
  ```