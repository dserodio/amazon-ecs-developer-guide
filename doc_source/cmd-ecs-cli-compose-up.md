# ecs\-cli compose up<a name="cmd-ecs-cli-compose-up"></a>

If an Amazon ECS task definition does not already exist, creates one from your compose file and runs one instance of that task on your cluster\.

**Important**  
Some features described may only be available with the latest version of the Amazon ECS CLI\. To obtain the latest version, see [Installing the Amazon ECS CLI](ECS_CLI_installation.md)\.

## Syntax<a name="cmd-ecs-cli-compose-up-syntax"></a>

ecs\-cli compose up \[\-\-region *region*\] \[\-\-cluster\-config *cluster\_config\_name*\] \[\-\-ecs\-profile *ecs\_profile*\] \[\-\-aws\-profile *aws\_profile*\] \[\-\-cluster *cluster\_name*\] \[\-\-launch\-type *launch\_type*\] \[\-\-create\-log\-groups\] \[\-\-force\-update\] \[\-\-help\] 

## Options<a name="cmd-ecs-cli-compose-up-options"></a>


| Name | Description | 
| --- | --- | 
|  `--region, -r region`  |  Specifies the AWS Region to use\. Defaults to the cluster configured using the configure command\. Type: String Required: No  | 
|  `--cluster-config cluster_config_name`  |  Specifies the name of the Amazon ECS cluster configuration to use\. Defaults to the cluster configuration set as the default\. Type: String Required: No  | 
|  `--ecs-profile ecs_profile`  |  Specifies the name of the Amazon ECS profile configuration to use\. Defaults to the profile configured using the configure profile command\. Type: String Required: No  | 
|  `--aws-profile aws_profile`  |  Specifies the AWS profile to use\. Enables you to use the AWS credentials from an existing named profile in `~/.aws/credentials`\. Type: String Required: No  | 
|  `--cluster, -c cluster_name`  |  Specifies the Amazon ECS cluster name to use\. Defaults to the cluster configured using the configure command\. Type: String Required: No  | 
|  `--launch-type launch_type`  |  Specifies the launch type to use\. Available options are `FARGATE` or `EC2`\. For more information about launch types, see [Amazon ECS Launch Types](launch_types.md)\. This overrides the default launch type stored in your cluster configuration\.  Type: StringRequired: No | 
|  `--force-update`  |  Forces the relaunching of the tasks\. Required: No  | 
|  `--help, -h`  |  Shows the help text for the specified command\. Required: No  | 

## Examples<a name="cmd-ecs-cli-compose-up-examples"></a>

### Register a Task Definition Using the AWS Fargate Launch Type with Task Networking<a name="cmd-ecs-cli-compose-up-example-1"></a>

This example creates a task definition with the project name `hello-world` from the `hello-world.yml` compose file\. Additional ECS parameters are specified for task and network configuration for the Fargate launch type\. Then one instance of the task is run using the Fargate launch type\.

Example Docker Compose file, named `hello-world.yml`:

```
version: '3'
services:
  nginx:
    image: nginx:latest
    ports:
      - "80:80"
    logging:
      driver: awslogs
      options: 
        awslogs-group: tutorial
        awslogs-region: us-east-1
        awslogs-stream-prefix: nginx
```

Example ECS parameters file, named `ecs-params.yml`:

```
version: 1
task_definition:
  ecs_network_mode: awsvpc
  task_execution_role: ecsTaskExecutionRole
  task_size:
    cpu_limit: 512
    mem_limit: 2GB
  services:
    nginx:
      essential: true
run_params:
  network_configuration:
    awsvpc_configuration:
      subnets:
        - subnet-abcd1234
        - subnet-dcba4321
      security_groups:
        - sg-abcd1234
        - sg-dcba4321
      assign_public_ip: ENABLED
```

Command:

```
ecs-cli compose --project-name hello-world --file hello-world.yml --ecs-params ecs-params.yml up --launch-type FARGATE
```

Output:

```
INFO[0000] Using ECS task definition                     TaskDefinition=ecscompose-hello-world:5
```