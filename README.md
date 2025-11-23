# tfmodule-google-subnets

Terraform module for creating and managing Google Cloud Platform subnets with comprehensive configuration options including VPC Flow Logs, secondary IP ranges, and IPv6 support.

## Features

- **Multi-Region Subnet Creation**: Deploy subnets across different GCP regions in a single configuration
- **VPC Flow Logs**: Comprehensive flow logging with customizable aggregation intervals, sampling rates, and metadata
- **Secondary IP Ranges**: Support for secondary IP ranges (ideal for GKE pod and service IP ranges)
- **IPv6 Support**: Dual-stack networking with IPv4 and IPv6 configuration options
- **Advanced Networking**: Custom subnet purposes, roles, and stack types for specialized use cases
- **Flexible Configuration**: Optional parameters with sensible defaults for rapid deployment
- **Enterprise Ready**: Production-grade subnet management with flow logs and monitoring capabilities

## Usage

### Basic Example
```hcl
module "subnets" {
  source = "github.com/your-org/tfmodule-google-subnets?ref=v1.0.0"

  project_id   = "my-project-id"
  network_name = "my-vpc-network"

  subnets = [
    {
      subnet_name   = "subnet-01"
      subnet_ip     = "10.0.1.0/24"
      subnet_region = "us-central1"
      description   = "Primary subnet for web tier"
    },
    {
      subnet_name   = "subnet-02"
      subnet_ip     = "10.0.2.0/24"
      subnet_region = "us-east1"
      description   = "Primary subnet for database tier"
    }
  ]
}
```

### Subnets with VPC Flow Logs
```hcl
module "subnets_with_flow_logs" {
  source = "github.com/your-org/tfmodule-google-subnets?ref=v1.0.0"

  project_id   = "my-project-id"
  network_name = "my-vpc-network"

  subnets = [
    {
      subnet_name               = "subnet-monitored"
      subnet_ip                 = "10.0.10.0/24"
      subnet_region             = "us-central1"
      description               = "Subnet with flow logs enabled"
      subnet_flow_logs          = "true"
      subnet_flow_logs_interval = "INTERVAL_10_SEC"
      subnet_flow_logs_sampling = "0.8"
      subnet_flow_logs_metadata = "INCLUDE_ALL_METADATA"
      subnet_flow_logs_filter   = "true"
    }
  ]
}
```

### Subnets with Secondary IP Ranges (GKE)
```hcl
module "gke_subnets" {
  source = "github.com/your-org/tfmodule-google-subnets?ref=v1.0.0"

  project_id   = "my-project-id"
  network_name = "my-vpc-network"

  subnets = [
    {
      subnet_name   = "gke-subnet"
      subnet_ip     = "10.0.0.0/20"
      subnet_region = "us-central1"
      description   = "GKE cluster subnet with secondary ranges"
    }
  ]

  secondary_ranges = {
    "gke-subnet" = [
      {
        range_name    = "gke-pods"
        ip_cidr_range = "10.4.0.0/14"
      },
      {
        range_name    = "gke-services"
        ip_cidr_range = "10.8.0.0/20"
      }
    ]
  }
}
```

### Dual-Stack Subnets (IPv4 and IPv6)
```hcl
module "ipv6_subnets" {
  source = "github.com/your-org/tfmodule-google-subnets?ref=v1.0.0"

  project_id   = "my-project-id"
  network_name = "my-vpc-network"

  subnets = [
    {
      subnet_name      = "dual-stack-subnet"
      subnet_ip        = "10.0.20.0/24"
      subnet_region    = "us-central1"
      description      = "Dual-stack subnet with IPv6"
      stack_type       = "IPV4_IPV6"
      ipv6_access_type = "EXTERNAL"
    }
  ]
}
```

### Proxy-Only Subnet (for Internal Load Balancers)
```hcl
module "proxy_subnets" {
  source = "github.com/your-org/tfmodule-google-subnets?ref=v1.0.0"

  project_id   = "my-project-id"
  network_name = "my-vpc-network"

  subnets = [
    {
      subnet_name   = "proxy-only-subnet"
      subnet_ip     = "10.0.30.0/24"
      subnet_region = "us-central1"
      description   = "Proxy-only subnet for Internal HTTP(S) Load Balancer"
      purpose       = "INTERNAL_HTTPS_LOAD_BALANCER"
      role          = "ACTIVE"
    }
  ]
}
```

### Complete Example with Custom Flow Logs Metadata
```hcl
module "enterprise_subnets" {
  source = "github.com/your-org/tfmodule-google-subnets?ref=v1.0.0"

  project_id   = "my-project-id"
  network_name = "my-vpc-network"

  subnets = [
    {
      subnet_name                      = "app-subnet"
      subnet_ip                        = "10.0.100.0/24"
      subnet_region                    = "us-central1"
      description                      = "Application tier subnet with custom flow logs"
      subnet_flow_logs                 = "true"
      subnet_flow_logs_interval        = "INTERVAL_5_SEC"
      subnet_flow_logs_sampling        = "1.0"
      subnet_flow_logs_metadata        = "CUSTOM_METADATA"
      subnet_flow_logs_filter          = "true"
      subnet_flow_logs_metadata_fields = [
        "src_vpc",
        "dest_vpc",
        "src_instance",
        "dest_instance"
      ]
    }
  ]

  secondary_ranges = {
    "app-subnet" = [
      {
        range_name    = "app-services"
        ip_cidr_range = "10.10.0.0/20"
      }
    ]
  }
}
```

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| project_id | The ID of the project where this VPC will be created | `string` | n/a | yes |
| network_name | The name of the network being created | `string` | n/a | yes |
| subnets | The list of subnets being created | `list(object)` | n/a | yes |
| secondary_ranges | Secondary ranges that will be used in some of the subnets | `map(list(object))` | `{}` | no |

### Subnets Object Structure

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| subnet_name | Name of the subnet | `string` | n/a | yes |
| subnet_ip | IP CIDR range for the subnet | `string` | n/a | yes |
| subnet_region | Region where the subnet will be created | `string` | n/a | yes |
| subnet_private_access | Enable Private Google Access | `string` | `"false"` | no |
| subnet_private_ipv6_access | IPv6 access type for private Google access | `string` | `null` | no |
| subnet_flow_logs | Enable VPC Flow Logs | `string` | `"false"` | no |
| subnet_flow_logs_interval | Aggregation interval for flow logs | `string` | `"INTERVAL_5_SEC"` | no |
| subnet_flow_logs_sampling | Sampling rate for flow logs (0.0 to 1.0) | `string` | `"0.5"` | no |
| subnet_flow_logs_metadata | Metadata format for flow logs | `string` | `"INCLUDE_ALL_METADATA"` | no |
| subnet_flow_logs_filter | Filter expression for flow logs | `string` | `"true"` | no |
| subnet_flow_logs_metadata_fields | Custom metadata fields (when metadata is CUSTOM_METADATA) | `list(string)` | `[]` | no |
| description | Description of the subnet | `string` | `null` | no |
| purpose | Purpose of the subnet (e.g., PRIVATE, INTERNAL_HTTPS_LOAD_BALANCER) | `string` | `null` | no |
| role | Role of the subnet (ACTIVE or BACKUP) | `string` | `null` | no |
| stack_type | Stack type for the subnet (IPV4_ONLY, IPV4_IPV6) | `string` | `null` | no |
| ipv6_access_type | IPv6 access type (EXTERNAL, INTERNAL) | `string` | `null` | no |

### Valid Values for Flow Logs Configuration

**subnet_flow_logs_interval**: `INTERVAL_5_SEC`, `INTERVAL_30_SEC`, `INTERVAL_1_MIN`, `INTERVAL_5_MIN`, `INTERVAL_10_MIN`, `INTERVAL_15_MIN`

**subnet_flow_logs_metadata**: `EXCLUDE_ALL_METADATA`, `INCLUDE_ALL_METADATA`, `CUSTOM_METADATA`

**stack_type**: `IPV4_ONLY`, `IPV4_IPV6`

**ipv6_access_type**: `EXTERNAL`, `INTERNAL`

**purpose**: `PRIVATE`, `INTERNAL_HTTPS_LOAD_BALANCER`, `REGIONAL_MANAGED_PROXY`, `GLOBAL_MANAGED_PROXY`

**role**: `ACTIVE`, `BACKUP`

## Outputs

| Name | Description |
|------|-------------|
| subnets | The created subnet resources containing all subnet attributes |


## Requirements

| Name | Version |
|------|---------|
| terraform | >= 1.5.0 |
| google | >= 7.0.0, < 8.0.0 |
| google-beta | >= 7.0.0, < 8.0.0 |

## Notes

- Subnets are uniquely identified by the combination of `subnet_region/subnet_name`
- Secondary ranges must reference existing subnet names defined in the `subnets` variable
- Flow logs sampling rate accepts values from 0.0 (no sampling) to 1.0 (100% sampling)
- When using `CUSTOM_METADATA` for flow logs, you must specify `subnet_flow_logs_metadata_fields`
- Proxy-only subnets cannot have secondary IP ranges
- IPv6 configuration requires the VPC network to have IPv6 ULA ranges enabled

## Changelog

See [CHANGELOG.md](./CHANGELOG.md) for version history and changes.