# terraform-provider-vault
[Terraform](https://www.terraform.io/) provider for reading secrets from [Hashicorp Vault](https://www.vaultproject.io/)

This provider allows you to retrieve secrets from a Vault instance and access them as variables within your Terraform configurations. It can provide convenient access to sensitive material, including:
* AWS credentials
* SSH keys
* SSL certificate keys

## Important Security Notice
This provider currently stores **unencrypted** secrets as plaintext in your `.tfstate` files! Do **not** use this module unless you have taken steps to ensure your `.tfstate` files are encrypted or otherwise secured. This provider should be considered unsafe until a solution has been found for [terraform:#516](https://github.com/hashicorp/terraform/issues/516).

## Usage

### Provider configuration
This module implements a `vault` provider.
```
provider "vault" {
    address = "https://vault:8200"
    app_id = "..."
    user_id = "..."
    token = "..."
}
```

##### Arguments

The Vault provider currently supports `token` and `app-id` authentication.

* `address` - (required) URL to the Vault API
* `token` - Explicit token for `token` authentication
* `app_id` - Application ID for `app-id` authentucation
* `user_id` - User ID for `app-id` authentication

### Resource configuration
This module implements a `vault_secret` resource.
```
resource "vault_secret" "aws" {
    path = "secret/aws/credentials"
}
```

##### Arguments
* `path` - The path to the secret store for this resource

The contents of `path` will be stored as a map in the `data` variable of the resource.

### Example
```
provider "vault" {
    address = "https://vault:8200"
    app_id = "eea928cc-2e83-4db7-8ad2-b90b7bd43542"
    user_id = "${file("~/.user-id")}"
}

resource "vault_secret" "aws" {
    path = "secret/aws/credentials"
}

// Assuming a Vault entry with the following fields:
// - access_key
// - secret__key
provider "aws" {
    access_key = "${vault_secret.aws.data.access_key}"
    secret_key = "${vault_secret.aws.data.secret_key}"
}
```

## Author

[Derek Moore](https://github.com/redredgroovy)
