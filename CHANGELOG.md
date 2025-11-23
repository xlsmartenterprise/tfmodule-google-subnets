# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2025-11-23

### Added
- Initial release of Google Cloud Subnet module
- Support for creating multiple subnets across different regions
- VPC Flow Logs configuration with customizable settings:
  - Aggregation interval
  - Flow sampling rate
  - Metadata format (including custom metadata fields)
  - Filter expressions
- Secondary IP ranges support for GKE pods and services
- IPv6 support with dual-stack configuration options
- Advanced subnet configurations:
  - Custom purpose (e.g., PRIVATE, INTERNAL_HTTPS_LOAD_BALANCER)
  - Role assignment (ACTIVE, BACKUP)
  - Stack type (IPV4_ONLY, IPV4_IPV6)
  - IPv6 access type configuration
- Optional subnet descriptions for documentation

### Outputs
- `subnets`: Complete subnet resource objects for downstream module integration

### Requirements
- Terraform >= 1.5.0
- Google Provider >= 7.0.0, < 8.0.0
- Google Beta Provider >= 7.0.0, < 8.0.0