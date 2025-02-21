# Terraform Module Documentation

## Networking Module

### Design Decisions

The networking module creates a production-ready VPC with the following considerations:

#### Multi-AZ Architecture
```hcl
variable "availability_zones" {
  description = "List of availability zones to use"
  type        = list(string)
  default     = ["us-east-1a", "us-east-1b", "us-east-1c"]
}
```

- Uses multiple AZs for high availability
- Distributes NAT Gateways across zones
- Implements subnet tiering for security

#### CIDR Allocation
```hcl
locals {
  public_subnet_cidrs  = [for i in range(length(var.availability_zones)) : 
                         cidrsubnet(var.vpc_cidr, 8, i)]
  private_subnet_cidrs = [for i in range(length(var.availability_zones)) : 
                         cidrsubnet(var.vpc_cidr, 8, i + length(var.availability_zones))]
}
```

- Dynamic CIDR calculation for subnets
- Non-overlapping ranges for clear separation
- Room for future expansion

#### Security Groups
- Principle of least privilege
- Specific ingress/egress rules
- Clear documentation of allowed traffic

### Usage Example

```hcl
module "networking" {
  source = "./modules/networking"
  
  environment        = "dev"
  vpc_cidr          = "10.0.0.0/16"
  availability_zones = ["us-east-1a", "us-east-1b"]
  
  # Optional: Override defaults
  enable_vpn_gateway = false
  single_nat_gateway = true  # Cost optimization for dev
}
```

