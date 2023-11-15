# Scalculator: A polyglot, microservice-oriented calculator

ℹ Looking for a microservice / service mesh sample app ? You might want to have a look at this.

## Services architecture

![architecture](diagrams/architecture.svg)

![api](diagrams/api.gif)

## Deploy to Kubernetes (Istio required)

**Requirements:** helm

**Commands:**

- Modify `helm/scalculator/values.yaml` and update your domain name
- Execute the following commands to deploy scalculator

```shell
$ kubectl create namespace scalculator
$ kubectl label namespace scalculator -l istio-injection=enabled
$ helm template helm/scalculator | kubectl apply -n scalculator -f -
```

The application will be accessible at:

- Parser service: http://APP_DOMAIN
  - GET /api/v1/status
  - POST /api/v1/calculate

**Run acceptance tests (as Job):**

```shell
$ kubectl run robot -n scalculator --image=foly/scalculator-robot --restart=OnFailure -l 'app=scalculator-robot'

$ export POD_NAME=$(kubectl get pod -l 'app=scalculator-robot' -n scalculator -o jsonpath --template='{.items[0].metadata.name}')
$ kubectl logs $POD_NAME -n scalculator
```

## Local run

**Requirements:** docker, docker-compose

**Commands:** `$ docker-compose up`

The application will be accessible at:

- Parser service: http://localhost:8080
  - GET /api/v1/status
  - POST /api/v1/calculate

**Run acceptance tests:**

```shell
$ docker-compose -f docker-compose.yml -f docker-compose.ci.yml run --rm robot
```

![robot](diagrams/robot.gif)

## Usage guide

- Parser service API

```
POST /api/v1/calculate HTTP/1.1
Content-Type: application/json
[...]

{
    "input": "1+1"
}

HTTP/1.1 200 OK
content-type: application/json
[...]

{
    "operands": [
        1,
        1
    ],
    "origins": [
        {
            "result": 1,
            "service": "name: parser, version: v1"
        },
        {
            "result": 1,
            "service": "name: parser, version: v1"
        }
    ],
    "result": 2,
    "service": "name: add, version: v1"
}
```
