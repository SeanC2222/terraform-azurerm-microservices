# Terraform module for standardizing microservice infrastructure provisioning

This module is to help standardize microservice provisioning

Documentation will be added soon

## Features

Provisioning multi-region microservices on Azure with minimal input to describe your service

Hosting Configurations:
* App Service
* Functions

Data Store Configurations:
* SQL Elastic Pool
* CosmosDB

Additional Configurations:
* Application Insights
* Storage
* Key Vault
* Deployment Slots
* Managed Identity
* Traffic Manager
* SQL Failover Group

## Structure

![architecture](documentation/microservice-architecture.PNG)

## Usage

For large services there are sometimes conflict issues between app service slots, function app slots, and traffic manager. To avoid these issues until this can be resolved you can run apply with the `-parallelism=1` argument

```
terraform apply -parallelism=1 "dev.tfplan"
```

```hcl

module "microservice" {
  source = "github.com/matthewsre/terraform-azurerm-microservices"

  service_name = "myservice"
  regions      = ["westus2", "eastus"]
  environment  = "dev"

  microservices = [
    {
      name       = "service1"
      appservice = "plan"
    },
    {
      name       = "service2"
      appservice = "plan"
      function   = "plan"
      cosmos_containers = [
        {
          name               = "container1"
          partition_key_path = "/PartitionKey"
          max_throughput     = 0
        }
      ]
    },
    {
      name       = "service3"
      appservice = "plan"
      function   = "consumption"
      sql        = "elastic"
    },
    {
      name       = "service4"
      appservice = "plan"
      sql        = "elastic"
    },
    {
      name       = "service5"
      appservice = "plan"
      cosmos_containers = [
        {
          name               = "container2"
          partition_key_path = "/PartitionKey"
          max_throughput     = 0
        }
      ]
    }
  ]
}

```
