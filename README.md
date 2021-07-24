# Tutorial how to deploy a PHP container to AWS ECS Fargete using a docker compose.yaml file

From a compose.yml file docker translate it to Cloudformation to deploy the solution.

[ecs-cli-tutorial-fargate](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-cli-tutorial-fargate.html "I used this tutorial to copy the compose.yml file")

#### Docker Context ECS

```bash

 docker context ls
NAME                TYPE                DESCRIPTION                               DOCKER ENDPOINT                             KUBERNETES ENDPOINT           ORCHESTRATOR
default             moby                Current DOCKER_HOST based configuration   npipe:////./pipe/docker_engine                                    swarm
desktop-linux       moby                                                          npipe:////./pipe/dockerDesktopLinuxEngine
ecscontext *        ecs                 credentials read from environment

```


```bash
>
bruno@OMEN30L#C:\\repos\\mydockerlabs\\composeToECS\\mydockerlabs
❯ docker compose up
[+] Running 14/14
 - mydockerlabs             CreateComplete      270.7s
 - LogGroup                 CreateComplete      3.0s
 - CloudMap                 CreateComplete      48.0s
 - WebTCP80TargetGroup      CreateComplete      1.0s
 - WebTaskExecutionRole     CreateComplete      13.0s
 - DefaultNetwork           CreateComplete      5.0s
 - Cluster                  CreateComplete      6.0s
 - DefaultNetworkIngress    CreateComplete      1.0s
 - LoadBalancer             CreateComplete      184.0s
 - Default80Ingress         CreateComplete      1.0s
 - WebTaskDefinition        CreateComplete      3.0s
 - WebServiceDiscoveryEntry CreateComplete      2.0s
 - WebTCP80Listener         CreateComplete      3.0s
 - WebService               CreateComplete      67.0s
bruno@OMEN30L#C:\\repos\\mydockerlabs\\composeToECS\\mydockerlabs
❯

❯ docker compose down

```

```bash

❯ docker compose ps -a
NAME                                                 SERVICE    STATUS  PORTS
task/mydockerlabs/d2776a0c0e374ff8af068351f4ac65c7   web        Running mydoc-LoadB-L0N0UA6MEPV9-1065324718.us-east-1.elb.amazonaws.com:80->80/http
```

```bash

❯ aws ecs list-clusters
{
    "clusterArns": [
        "arn:aws:ecs:us-east-1:885248273567:cluster/mydockerlabs"
    ]
}

❯ aws ecs describe-clusters --cluster arn:aws:ecs:us-east-1:885248273567:cluster/mydockerlabs
{
    "clusters": [
        {
            "clusterArn": "arn:aws:ecs:us-east-1:885248273567:cluster/mydockerlabs",
            "clusterName": "mydockerlabs",
            "status": "ACTIVE",
            "registeredContainerInstancesCount": 0,
            "runningTasksCount": 1,
            "pendingTasksCount": 0,
            "activeServicesCount": 1,
            "statistics": [],
            "tags": [],
            "settings": [
                {
                    "name": "containerInsights",
                    "value": "disabled"
                }
            ],
            "capacityProviders": [],
            "defaultCapacityProviderStrategy": []
        }
    ],
    "failures": []
}

❯ aws ecs list-services --cluster arn:aws:ecs:us-east-1:885248273567:cluster/mydockerlabs
{
    "serviceArns": [
        "arn:aws:ecs:us-east-1:885248273567:service/mydockerlabs/mydockerlabs-WebService-BKsnNzwe3LEG"
    ]
}

❯ aws ecs list-tasks --cluster arn:aws:ecs:us-east-1:885248273567:cluster/mydockerlabs
{
    "taskArns": [
        "arn:aws:ecs:us-east-1:885248273567:task/mydockerlabs/d2776a0c0e374ff8af068351f4ac65c7"
    ]
}


```


```bash
❯ aws ecs describe-services --services arn:aws:ecs:us-east-1:885248273567:service/mydockerlabs/mydockerlabs-WebService-BKsnNzwe3LEG --cluster arn:aws:ecs:us-east-1:885248273567:cluster/mydockerlabs
{
    "services": [
        {
            "serviceArn": "arn:aws:ecs:us-east-1:885248273567:service/mydockerlabs/mydockerlabs-WebService-BKsnNzwe3LEG",
            "serviceName": "mydockerlabs-WebService-BKsnNzwe3LEG",
            "clusterArn": "arn:aws:ecs:us-east-1:885248273567:cluster/mydockerlabs",
            "loadBalancers": [
                {
                    "targetGroupArn": "arn:aws:elasticloadbalancing:us-east-1:885248273567:targetgroup/mydoc-WebTC-4ZK29GGVX84S/0842a87bba1861d2",
                    "containerName": "web",
                    "containerPort": 80
                }
            ],
            "serviceRegistries": [
                {
                    "registryArn": "arn:aws:servicediscovery:us-east-1:885248273567:service/srv-l6qmru2ww7pvs2xh"
                }
            ],
            "status": "ACTIVE",
            "desiredCount": 1,
            "runningCount": 1,
            "pendingCount": 0,
            "launchType": "FARGATE",
            "platformVersion": "1.4.0",
            "taskDefinition": "arn:aws:ecs:us-east-1:885248273567:task-definition/mydockerlabs-web:1",
            "deploymentConfiguration": {
                "deploymentCircuitBreaker": {
                    "enable": false,
                    "rollback": false
                },
                "maximumPercent": 200,
                "minimumHealthyPercent": 100
            },
            "deployments": [
                {
                    "id": "ecs-svc/3556177981863873465",
                    "status": "PRIMARY",
                    "taskDefinition": "arn:aws:ecs:us-east-1:885248273567:task-definition/mydockerlabs-web:1",
                    "desiredCount": 1,
                    "pendingCount": 0,
                    "runningCount": 1,
                    "failedTasks": 0,
                    "createdAt": "2021-07-24T01:56:27.261000-06:00",
                    "updatedAt": "2021-07-24T01:57:51.413000-06:00",
                    "launchType": "FARGATE",
                    "platformVersion": "1.4.0",
                    "networkConfiguration": {
                        "awsvpcConfiguration": {
                            "subnets": [
                                "subnet-01424c8671251e5dc",
                                "subnet-0c9bd53f583874691"
                            ],
                            "securityGroups": [
                                "sg-047f292a15a0d885c"
                            ],
                            "assignPublicIp": "ENABLED"
                        }
                    },
                    "rolloutState": "COMPLETED",
                    "rolloutStateReason": "ECS deployment ecs-svc/3556177981863873465 completed."
                }
            ],
            "roleArn": "arn:aws:iam::885248273567:role/aws-service-role/ecs.amazonaws.com/AWSServiceRoleForECS",
            "events": [
                {
                    "id": "b4193245-7573-481e-9327-efa115c877f1",
                    "createdAt": "2021-07-24T01:57:51.421000-06:00",
                    "message": "(service mydockerlabs-WebService-BKsnNzwe3LEG) has reached a steady state."
                },
                {
                    "id": "34bce40d-6f7c-441f-b1b6-b6f213fde6b0",
                    "createdAt": "2021-07-24T01:57:51.420000-06:00",
                    "message": "(service mydockerlabs-WebService-BKsnNzwe3LEG) (deployment ecs-svc/3556177981863873465) deployment completed."
                },
                {
                    "id": "d4f43f41-56d5-475c-932a-f7b0119afe1a",
                    "createdAt": "2021-07-24T01:57:31.026000-06:00",
                    "message": "(service mydockerlabs-WebService-BKsnNzwe3LEG) registered 1 targets in (target-group arn:aws:elasticloadbalancing:us-east-1:885248273567:targetgroup/mydoc-WebTC-4ZK29GGVX84S/0842a87bba1861d2)"
                },
                {
                    "id": "c9744f99-28c4-4f5d-9471-20b0e5c5d552",
                    "createdAt": "2021-07-24T01:56:28.535000-06:00",
                    "message": "(service mydockerlabs-WebService-BKsnNzwe3LEG) has started 1 tasks: (task d2776a0c0e374ff8af068351f4ac65c7)."
                }
            ],
            "createdAt": "2021-07-24T01:56:27.261000-06:00",
            "placementConstraints": [],
            "placementStrategy": [],
            "networkConfiguration": {
                "awsvpcConfiguration": {
                    "subnets": [
                        "subnet-01424c8671251e5dc",
                        "subnet-0c9bd53f583874691"
                    ],
                    "securityGroups": [
                        "sg-047f292a15a0d885c"
                    ],
                    "assignPublicIp": "ENABLED"
                }
            },
            "healthCheckGracePeriodSeconds": 0,
            "schedulingStrategy": "REPLICA",
            "createdBy": "arn:aws:iam::885248273567:role/aws-reserved/sso.amazonaws.com/us-west-2/AWSReservedSSO_AdministratorAccess_d5a459a5df554235",
            "enableECSManagedTags": false,
            "propagateTags": "SERVICE",
            "enableExecuteCommand": false
        }
    ],
    "failures": []
}

```




