# Confluent Kafka Cluster(Official)

https://www.confluent.io/confluent-cloud/

https://registry.terraform.io/providers/confluentinc/confluent/latest/docs

## What does this do?

Creates a Confluent Cloud Kafka cluster, topics, service accounts, role bindings

## How to use this module?

```hcl
module "confluent-kafka-cluster" {
  source                            = "github.com/dapperlabs-platform/terraform-confluent-official-kafka-cluster?ref=tag"
  environment              = "DEMO"
  gcp_region               = var.default_region
  name                     = "cruise"

  # OPTIONAL: Only needed when creating dedicated cluster, if not provided defaults to 2
  cku                      = 2

  # you must provide one of: basic, standard or dedicated
  cluster_tier             = "DEDICATED"

  # Note Basic Cluster cannot have MULTI_ZONE availability just SINGLE_ZONE
  availability             = "MULTI_ZONE"

  kafka_lag_exporter_image_version = "0.7.1"
  ccloud_exporter_image_version = "0.7.1"
  
  topics = {
    "topic-1" = {
      replication_factor = 3
      partitions         = 1
      config = {
        "cleanup.policy" = "delete"
      }
      acl_readers = ["user1"]
      acl_writers = ["user2"]
    }
  }
}
```

## Resources created

- 1 Confluent Cloud environment
- 1 Kafka cluster
- 1 Service account for each distinct entry in `acl_readers` and `acl_writers` variables
- Topics

## Requirements

- Terraform >= 1.0.0
- confluentinc/confluent >= 1.0.0

## Inputs

| Name                                                                                                                                                                                                                                    | Description                                                                                                                                                           | Type | Default | Required |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |--------------------------------------------------------------------------------------------------------------| -- | ---- | :------: |
| name                                                                                                                                                                                                                                    | Kafka cluster identifier. Will be prepended by the environment value in Confluent cloud                                       | string |      |    x     |
| environment                                                                                                                                                                                                                         | Application environment that uses the cluster                                                                                                       | string |      |    x     |
| gcp_region                                                                                                                                                                                                                           | GCP region in which to deploy the cluster. See https://docs.confluent.io/cloud/current/clusters/regions.html      | string |      |    x     |
| availability                                                                                                                                                                                                                            | Cluster availability. SINGLE_ZONE or MULTI_ZONE                                                                                                  | string | MULTI_ZONE     |          |
| service_provider                                                                                                                                                                                                                   | Confluent cloud service provider. AWS, GCP, Azure                                                                                                | string | GCP  |          |                                                                                                                                                                                                                   | Grafana datasource to use in dashboards                                                                      | string |         |   null   |