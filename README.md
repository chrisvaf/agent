[![Build Status](https://travis-ci.com/optimizely/agent.svg?token=y3xM1z7bQsqHX2NTEhps&branch=master)](https://travis-ci.com/optimizely/agent)
[![Coverage Status](https://coveralls.io/repos/github/optimizely/agent/badge.svg)](https://coveralls.io/github/optimizely/agent)
# Optimizely Agent
Optimizely Agent is a service which exposes the functionality of the Full Stack and Rollouts SDKs as
a highly available and distributed web application.

Optimizely Full Stack is A/B testing and feature flag management for product development teams. Experiment in any application. Make every feature on your roadmap an opportunity to learn. Learn more at https://www.optimizely.com/platform/full-stack/, or see the [documentation](https://docs.developers.optimizely.com/full-stack/docs).

## Getting Started
To get started with Optimizely Agent, follow the [Quickstart guide](https://docs.developers.optimizely.com/full-stack/docs/) and view example usage in our [examples folder](./examples).

## Prerequisites
Optimizely Agent is implemented in [Golang](https://golang.org/). Golang version 1.13+ is required for developing and compiling from source.
Installers and binary archives for most platforms can be downloaded directly from the Go [downloads](https://golang.org/dl/) page.

## Running from source (Linux / OSX)
Once Go is installed, the Optimizely Agent can be started via the following `make` command:
```bash
make setup
make run
```
This will start the Optimizely Agent with the default configuration in the foreground.

## Running from source (Windows)
A helper script is available under [scripts/build.ps1](./scripts/build.ps1) to automate compiling Agent in a Windows environment. The script will download and install both Git and Golang and then attempt to compile Agent. Open a Powershell terminal and run 
```bash
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

.\scripts\build.ps1

.\bin\optimizely.exe
```

## Running via Docker
If you have Docker installed, Optimizely Agent can be started as a container. First pull the Docker image with:
```bash
docker pull optimizely/agent
```

By default this will pull the "latest" tag. You can also specify a specific version of Agent by providing the version
as a tag to the docker command:
```bash
docker pull optimizely/agent:X.Y.Z
```

Then run the docker container with:
```bash
docker run -p 8080:8080 --env OPTIMIZELY_LOG_PRETTY=true --env OPTIMIZELY_SERVER_HOST=0.0.0.0 --env OPTIMIZELY_SERVER_ALLOWEDHOSTS=127.0.0.1 optimizely/agent
```

This will start Agent in the foreground and expose the container API port 8080 to the host.

Note that when a new version is released, 2 images are pushed to dockerhub, they are distinguished by their tags:
- :latest (same as :X.Y.Z)
- :alpine (same as :X.Y.Z-alpine)

The difference between latest and alpine is that latest is built `FROM scratch` while alpine is `FROM alpine`.
- [latest Dockerfile](./scripts/dockerfiles/Dockerfile.static)
- [alpine Dockerfile](./scripts/dockerfiles/Dockerfile.alpine)

## Configuration Options
Optimizely Agent configuration can be overridden by a yaml configuration file provided at runtime.

By default the configuration file will be sourced from the current active directory `e.g. ./config.yaml`.
Alternative configuration locations can be specified at runtime via environment variable or command line flag.
```bash
OPTIMIZELY_CONFIG_FILENAME=config.yaml make run
```
The default configuration can be found [here](config.yaml).

Below is a comprehensive list of available configuration properties.

|Property Name|Env Variable|Description|
|---|---|---|
|admin.auth.clients|N/A|Credentials for requesting access tokens. See: [Authorization Guide](https://docs.developers.optimizely.com/full-stack/docs/authorization)|
|admin.auth.hmacSecrets|OPTIMIZELY_ADMIN_AUTH_HMACSECRETS|Signing secret for issued access tokens. See: [Authorization Guide](https://docs.developers.optimizely.com/full-stack/docs/authorization)|
|admin.auth.jwksUpdateInterval|OPTIMIZELY_ADMIN_AUTH_JWKSUPDATEINTERVAL|JWKS Update Interval for caching the keys in the background. See: [Authorization Guide](https://docs.developers.optimizely.com/full-stack/docs/authorization)|
|admin.auth.jwksURL|OPTIMIZELY_ADMIN_AUTH_JWKSURL|JWKS URL for validating access tokens. See: [Authorization Guide](https://docs.developers.optimizely.com/full-stack/docs/authorization)|
|admin.auth.ttl|OPTIMIZELY_ADMIN_AUTH_TTL|Time-to-live of issued access tokens. See: [Authorization Guide](https://docs.developers.optimizely.com/full-stack/docs/authorization)|
|admin.port|OPTIMIZELY_ADMIN_PORT|Admin listener port. Default: 8088|
|api.auth.clients|N/A|Credentials for requesting access tokens. See: [Authorization Guide](https://docs.developers.optimizely.com/full-stack/docs/authorization)|
|api.auth.hmacSecrets|OPTIMIZELY_API_AUTH_HMACSECRETS|Signing secret for issued access tokens. See: [Authorization Guide](https://docs.developers.optimizely.com/full-stack/docs/authorization)|
|api.auth.jwksUpdateInterval|OPTIMIZELY_API_AUTH_JWKSUPDATEINTERVAL|JWKS Update Interval for caching the keys in the background. See: [Authorization Guide](https://docs.developers.optimizely.com/full-stack/docs/authorization)|
|api.auth.jwksURL|OPTIMIZELY_API_AUTH_JWKSURL|JWKS URL for validating access tokens. See: [Authorization Guide](https://docs.developers.optimizely.com/full-stack/docs/authorization)|
|api.auth.ttl|OPTIMIZELY_API_AUTH_TTL|Time-to-live of issued access tokens. See: [Authorization Guide](https://docs.developers.optimizely.com/full-stack/docs/authorization)|
|api.enableNotifications|OPTIMIZELY_API_ENABLENOTIFICATIONS|Enable streaming notification endpoint. Default: false|
|api.enableOverrides|OPTIMIZELY_API_ENABLEOVERRIDES|Enable bucketing overrides endpoint. Default: false|
|api.maxConns|OPTIMIZELY_API_MAXCONNS|Maximum number of concurrent requests|
|api.port|OPTIMIZELY_API_PORT|Api listener port. Default: 8080|
|author|OPTIMIZELY_AUTHOR|Agent author. Default: Optimizely Inc.|
|client.batchSize|OPTIMIZELY_CLIENT_BATCHSIZE|The number of events in a batch. Default: 10|
|client.datafileURLTemplate|OPTIMIZELY_CLIENT_DATAFILEURLTEMPLATE|Template URL for SDK datafile location. Default: https://cdn.optimizely.com/datafiles/%s.json|
|client.eventURL|OPTIMIZELY_CLIENT_EVENTURL|URL for dispatching events. Default: https://logx.optimizely.com/v1/events|
|client.flushInterval|OPTIMIZELY_CLIENT_FLUSHINTERVAL|The maximum time between events being dispatched. Default: 30s|
|client.pollingInterval|OPTIMIZELY_CLIENT_POLLINGINTERVAL|The time between successive polls for updated project configuration. Default: 1m|
|client.queueSize|OPTIMIZELY_CLIENT_QUEUESIZE|The max number of events pending dispatch. Default: 1000|
|client.sdkKeyRegex|OPTIMIZELY_CLIENT_SDKKEYREGEX|Regex to validate SDK keys provided in request header. Default: ^\\w+(:\\w+)?$|
|client.userProfileService|OPTIMIZELY_CLIENT_USERPROFILESERVICE| Property used to enable and set UserProfileServices. Default: ./config.yaml|
|config.filename|OPTIMIZELY_CONFIG_FILENAME|Location of the configuration YAML file. Default: ./config.yaml|
|log.level|OPTIMIZELY_LOG_LEVEL|The log [level](https://github.com/rs/zerolog#leveled-logging) for the agent. Default: info|
|log.pretty|OPTIMIZELY_LOG_PRETTY|Flag used to set colorized console output as opposed to structured json logs. Default: false|
|name|OPTIMIZELY_NAME|Agent name. Default: optimizely|
|sdkKeys|OPTIMIZELY_SDKKEYS|Comma delimited list of SDK keys used to initialize on startup|
|server.allowedHosts|OPTIMIZELY_SERVER_ALLOWEDHOSTS|List of allowed request host values. Requests whose host value does not match either the configured server.host, or one of these, will be rejected with a 404 response. To match all subdomains, you can use a leading dot (for example `.example.com` matches `my.example.com`, `hello.world.example.com`, etc.). You can use the value `.` to disable allowed host checking, allowing requests with any host. Request host is determined in the following priority order: 1. X-Forwarded-Host header value, 2. Forwarded header host= directive value, 3. Host property of request (see Host under https://golang.org/pkg/net/http/#Request). Note: don't include port in these hosts values - port is stripped from the request host before comparing against these.|
|server.batchRequests.maxConcurrency|OPTIMIZELY_SERVER_BATCHREQUESTS_MAXCONCURRENCY|Number of requests running in parallel. Default: 10|
|server.batchRequests.operationsLimit|OPTIMIZELY_SERVER_BATCHREQUESTS_OPERATIONSLIMIT|Number of allowed operations. ( will flag an error if the number of operations exeeds this parameter) Default: 500|
|server.certfile|OPTIMIZELY_SERVER_CERTFILE|Path to a certificate file, used to run Agent with HTTPS|
|server.disabledCiphers|OPTIMIZELY_SERVER_DISABLEDCIPHERS|List of TLS ciphers to disable when accepting HTTPS connections|
|server.healthCheckPath|OPTIMIZELY_SERVER_HEALTHCHECKPATH|Path for the health status api. Default: /health|
|server.host|OPTIMIZELY_SERVER_HOST|Host of server. Default: 127.0.0.1|
|server.interceptors|N/A|Property used to enable and set [Interceptor](https://docs.developers.optimizely.com/full-stack/docs/agent-plugins#section-interceptor-plugins) plugins|
|server.keyfile|OPTIMIZELY_SERVER_KEYFILE|Path to a key file, used to run Agent with HTTPS|
|server.readTimeout|OPTIMIZELY_SERVER_READTIMEOUT|The maximum duration for reading the entire body. Default: “5s”|
|server.writeTimeout|OPTIMIZELY_SERVER_WRITETIMEOUT|The maximum duration before timing out writes of the response. Default: “10s”|
|version|OPTIMIZELY_VERSION|Agent version. Default: `git describe --tags`|
|webhook.port|OPTIMIZELY_WEBHOOK_PORT|Webhook listener port: Default: 8085|
|webhook.projects.<*projectId*>.sdkKeys|N/A|Comma delimited list of SDK Keys applicable to the respective projectId|
|webhook.projects.<*projectId*>.secret|N/A|Webhook secret used to validate webhook requests originating from the respective projectId|
|webhook.projects.<*projectId*>.skipSignatureCheck|N/A|Boolean to indicate whether the signature should be validated. TODO remove in favor of empty secret.|

More information about configuring Agent can be found in the [Advanced Configuration Notes](https://docs.developers.optimizely.com/full-stack/docs/advanced-configuration).

## API

The core API is implemented as a REST service configured on it's own HTTP listener port (default 8080).
The full API specification is defined in an OpenAPI 3.0 (aka Swagger) [spec](./api/openapi-spec/openapi.yaml).

Each request made into the API must include a `X-Optimizely-SDK-Key` in the request header to
identify the context the request should be evaluated. The SDK key maps to a unique Optimizely Project and
[Environment](https://docs.developers.optimizely.com/rollouts/docs/manage-environments) allowing multiple
Environments to be serviced by a single Agent. 
For a secure environment, this header should include both the SDK key and the datafile access token 
separated by a colon. For example, if SDK key is `my_key` and datafile access token is `my_token` 
then set header's value to `my_key:my_token`.  

### Enabling CORS

CORS can be enabled for the core API service by setting the the appropriate cors properties.
```yaml
api:
  cors:
    allowedMethods:
      - "HEAD"
      - "GET"
      - "POST"
      - "OPTIONS"
```

For more advanced options please refer to the [go-chi/cors](https://github.com/go-chi/cors) middleware documentation.

NOTE: To avoid any potential security issues, and reduce risk to your data it's recommended that [authentication](https://docs.developers.optimizely.com/full-stack/docs/authorization)
is enabled alongside CORS.

## Webhooks

The webhook listener used to receive inbound [Webhook](https://docs.developers.optimizely.com/rollouts/docs/webhooks)
requests from optimizely.com. These webhooks enable PUSH style notifications triggering immediate project configuration updates.
The webhook listener is configured on its own port (default: 8085) since it can be configured to select traffic from the internet.

To accept webhook requests Agent must be configured by mapping an Optimizely Project Id to a set of SDK keys along
with the associated secret used for validating the inbound request. An example webhook configuration can
be found in the the provided [config.yaml](./config.yaml).

## Admin API

The Admin API provides system information about the running process. This can be used to check the availability
of the service, runtime information and operational metrics. By default the admin listener is configured on port 8088.

### Info

The `/info` endpoint provides basic information about the Optimizely Agent instance.

Example Request:
```bash
curl localhost:8088/info
```

Example Response:
```json
{
    "version": "v0.10.0",
    "author": "Optimizely Inc.",
    "app_name": "optimizely"
}
```

### Health Check

The `/health` endpoint is used to determine service availability.

Example Request:
```bash
curl localhost:8088/health
```

Example Response:
```json
{
    "status": "ok"
}
```

Agent will return a HTTP 200 - OK response if and only if all configured listeners are open and all external dependent services can be reached.
A non-healthy service will return a HTTP 503 - Unavailable response with a descriptive message to help diagnose the issue.

This endpoint can used when placing Agent behind a load balancer to indicate whether a particular instance can receive inbound requests.

### Metrics

The `/metrics` endpoint exposes telemetry data of the running Optimizely Agent. The core runtime metrics are exposed via the go expvar package. Documentation for the various statistics can be found as part of the [mstats](https://golang.org/src/runtime/mstats.go) package.

Example Request:
```bash
curl localhost:8088/metrics
```

Example Response:
```
{
    "cmdline": [
        "bin/optimizely"
    ],
    "memstats": {
        "Alloc": 924136,
        "TotalAlloc": 924136,
        "Sys": 71893240,
        "Lookups": 0,
        "Mallocs": 4726,
        "HeapAlloc": 924136,
        ...
        "Frees": 172
    },
    ...
}
```
Custom metrics are also provided for the individual service endpoints and follow the pattern of:

```
"timers.<metric-name>.counts": 0,
"timers.<metric-name>.responseTime": 0,
"timers.<metric-name>.responseTimeHist.p50": 0,
"timers.<metric-name>.responseTimeHist.p90": 0,
"timers.<metric-name>.responseTimeHist.p95": 0,
"timers.<metric-name>.responseTimeHist.p99": 0,
```

### Profiling

Agent exposes the runtime profiling data in the format expected by the [pprof](https://github.com/google/pprof/blob/master/doc/README.md) visualization tool.

You can use the pprof tool to look at the heap profile:

```
go tool pprof http://localhost:6060/debug/pprof/heap
```

Or to look at a 5-second CPU profile: (higher durations require configuring the `server.writeTimeout`)

```
go tool pprof http://localhost:6060/debug/pprof/profile?seconds=5
```

Or to look at the goroutine blocking profile, after setting `runtime.blockProfileRate` in the configuration:

```
go tool pprof http://localhost:8088/debug/pprof/block
```

Or to collect a 5-second execution trace:

```
wget "http://localhost:8088/debug/pprof/trace?seconds=5"
```

Or to look at the holders of contended mutexes, after setting `runtime.mutexProfileFraction` in your configuration:

```
go tool pprof http://localhost:6060/debug/pprof/mutex
```

To view all available profiles can be found at [http://localhost:8088/debug/pprof/](http://localhost:8088/debug/pprof/) in your browser.

## Agent Plugins
Optimizely Agent can be extended through the use of [plugins](https://docs.developers.optimizely.com/full-stack/docs/agent-plugins). Plugins are distinct from the standard Agent packages
to provide a namespaced environment for custom logic. Plugins must be compiled as part of the Agent distribution and are enabled through configuration.

### Interceptor Plugins
* [httplog](./plugins/interceptors/httplog) - Adds HTTP request logging based on [go-chi/httplog](https://github.com/go-chi/httplog).

## UserProfileService Plugins
* [UserProfileService](./plugins/userprofileservice/README.md) - Adds UserProfileService.

## Authorization
Optimizely Agent supports authorization workflows based on OAuth and JWT standards, allowing you to protect access to its API and Admin interfaces. For details, see [Authorization Guide](https://docs.developers.optimizely.com/full-stack/docs/authorization).

## Notifications
Just as you can use Notification Listeners to subscribe to events of interest with Optimizely SDKs, you can use the Notifications endpoint to subscribe to events in Agent. For more information, see the [Notifications Guide](https://docs.developers.optimizely.com/full-stack/docs/agent-notifications).

## Package Structure
Following best practice for go project layout as defined [here](https://github.com/golang-standards/project-layout)

* **api** - OpenAPI/Swagger specs, JSON schema files, protocol definition files.
* **bin** - Compiled application binaries.
* **cmd** - Main applications for this project.
* **config** - Application configuration.
* **docs** - User documentation files.
* **pkg** - Library code that can be used by other applications.
* **plugins** - Plugin libraries for extending Agent functionality.
* **scripts** - Scripts to perform various build, install, analysis, etc operations.

## Make targets
The following `make` targets can be used to build and run the application:
* **build** - builds optimizely and installs binary in bin/optimizely
* **clean** - runs `go clean` and removes the bin/ dir
* **cover** - runs test suite with coverage profiling
* **cover-html** - generates test coverage html report
* **install** - installs all dev and ci dependencies, but does not install golang
* **lint** - runs `golangci-lint` linters defined in `.golangci.yml` file
* **run** - builds and executes the optimizely binary
* **test** - recursively tests all .go files

## Credits

This software is used with additional code that is separately downloaded by you. These components are subject to their own license terms which you should review carefully.

Gohistogram
(c) 2013 VividCortex 
License (MIT): github.com/VividCortex/gohistogram

Chi
(c) 2015-present Peter Kieltyka (https://github.com/pkieltyka), Google Inc. 
License (MIT): github.com/go-chi/chi

chi-render 
(c) 2016-Present https://github.com/go-chi ‑ authors
License (MIT): github.com/go-chi/render

hostrouter
(c) 2016-Present https://github.com/go-chi - authors
License (MIT): https://github.com/go-chi/hostrouter

go-chi/cors
(c) 2014 Olivier Poitrey
License (MIT): github.com/go-chi/cors

go-kit 
(c) 2015 Peter Bourgon
License (MIT): github.com/go-kit/kit

guuid
(c) 2009,2014 Google Inc. All rights reserved. 
License (BSD 3-Clause): github.com/google/uuid

optimizely go sdk 
(c) 2016-2017, Optimizely, Inc. and contributors
License (Apache 2): github.com/optimizely/go-sdk

concurrent-map 
(c) 2014 streamrail
License (MIT): github.com/orcaman/concurrent-map

zerolog 
(c) 2017 Olivier Poitrey
License (MIT): github.com/rs/zerolog

viper 
(c) 2014 Steve Francia
License (MIT): github.com/spf13/viper

testify
(c) 2012-2018 Mat Ryer and Tyler Bunnell
License (MIT): github.com/stretchr/testify

net 
(c) 2009 The Go Authors
License (BSD 3-Clause): https://github.com/golang/net

sync 
(c) 2009 The Go Authors
License (BSD 3-Clause): https://github.com/golang/sync

statik
(c) 2014 rakyll
License (Apache 2): github.com/rakyll/statik v0.1.7

sys 
(c) 2009 The Go Authors
License (BSD 3-Clause): https://github.com/golang/sys

## Apache Copyright Notice
Copyright 2019-present, Optimizely, Inc. and contributors

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at http://www.apache.org/licenses/LICENSE-2.0
Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS,   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.
