# terraform-aviatrix-azure-controlplane

### Description
This module deploys the Aviatrix control plane, or individual parts thereof.

### Quick Start with CloudShell
For users who want to deploy quickly without managing Terraform state or complex configurations, we provide a simplified CloudShell deployment script that wraps this module. This option is perfect for getting started quickly or for one-time deployments.

👉 **[CloudShell Deployment Guide](./cloudshell/README.md)**

### Requirements
This module assumes you have Azure CLI installed and are authenticated with sufficient privileges.

### Compatibility
Module version | Terraform version
:--- | :---
v1.2.1 | >= 1.3.0

### Using an existing service principal
By default the module creates a new Azure AD application and service principal (named by `app_name`) and onboards it to the controller. To onboard a pre-existing service principal instead, disable app registration and pass its credentials:
```hcl
module "control_plane" {
  source  = "terraform-aviatrix-modules/azure-controlplane/aviatrix"
  version = "1.2.1"

  # ...other inputs...

  module_config = {
    accept_controller_subscription = true,
    accept_copilot_subscription    = true,
    controller_deployment          = true,
    controller_initialization      = true,
    copilot_deployment             = true,
    copilot_initialization         = true,
    app_registration               = false,
    account_onboarding             = true,
  }

  existing_app_credentials = {
    subscription_id = var.sub_id
    directory_id    = var.tenant_id
    client_id       = var.spn_client_id
    client_secret   = var.spn_client_secret # e.g. TF_VAR_spn_client_secret
  }
}
```
`app_name`, `app_password_validity_length`, `create_custom_role`, `subscription_ids`, `aviatrix_rgs` and `aviatrix_role_names` are ignored in this mode. The module does not create role assignments for an existing service principal, so make sure it already holds the roles Aviatrix requires.
