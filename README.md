# Module - Azure Storage Account
[![COE](https://img.shields.io/badge/Created%20By-CCoE-blue)]()
[![HCL](https://img.shields.io/badge/language-HCL-blueviolet)](https://www.terraform.io/)
[![Azure](https://img.shields.io/badge/provider-Azure-blue)](https://registry.terraform.io/providers/hashicorp/azurerm/latest)

Module developed to standardize the creation of Azure Storage Accounts and containers. 
With this module you can apply rbac on containers (Storage Blob Data Contributor) referring Azure AD Groups. 
This module can create the rbac for the container used by static web sites ($web) referring Azure AD Groups. 

## Compatibility Matrix

| Module Version | Terraform Version | AzureRM Version |
|----------------|-------------------| --------------- |
| v1.0.0         | v1.4.6            | 3.57.0          |

## Specifying a version

To avoid that your code get updates automatically, is mandatory to set the version using the `source` option. 
By defining the `?ref=***` in the the URL, you can define the version of the module.

Note: The `?ref=***` refers a tag on the git module repo.

## Use case

```hcl
module "<storage-account-name>" {
  source                   = "git::https://github.com/Azure/terraform-azurerm-storage-account?ref=v1.0.0"
  name                     = <storage-account-name>
  location                 = <region>
  resource_group_name      = <resource-group-name>
  account_kind             = "StorageV2"
  account_tier             = "Standard"
  access_tier              = "Hot"
  account_replication_type = "LRS"
  tags = {
    "key1"  = "value1"
    "key2"        = "value2"
  }
  container = {    
      name                  = "<container-name>",
      container_access_type = "<private>"
      ad_group              = "<azure group ad>"    
  }
}
output "name" {
  value = module.storage-account-name.name
}
output "id" {
  value = module.storage-account-name.id
}
```

## Input variables

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| name | storage account name | `string` | n/a | `Yes` |
| resource_group_name | resource group where the ACR will be placed | `string` | n/a | `Yes` |
| location | azure region | `string` | n/a | `Yes` |
| account_kind | defines the kind of account | `string` | `StorageV2` | No |
| account_tier | defines the tier to use for this storage account | `string` | n/a | `Yes` |
| account_replication_type | defines the type of replication to use for this storage account | `string` | n/a | `Yes` |
| cross_tenant_replication_enabled | should cross tenant replication be enabled? | `bool` | `True` | No |
| access_tier | defines the access tier for the storage accounts | `string` | `Hot` | No |
| edge_zone | specifies the edge zone within the azure region where this storage account should exist | `string` | `null` | No |
| enable_https_traffic_only | boolean flag which forces HTTPS if enabled, see here for more information. | `bool` | `True` | No |
| min_tls_version | the minimum supported TLS version for the storage account | `string` | `TLS1_2` | No |
| allow_nested_items_to_be_public | Allow or disallow nested items within this account to opt into being public | `bool` | `false` | No |
| shared_access_key_enabled | indicates whether the storage account permits requests to be authorized with the account access key via shared key | `bool` | `True` | No |
| public_network_access_enabled | whether the public network access is enabled? | `bool` | `True` | No |
| default_to_oauth_authentication | default to Azure Active Directory authorization in the Azure portal when accessing the storage account | `bool` | `False` | No |
| nfsv3_enabled | Is NFSv3 protocol enabled | `bool` | `False` | No |
| custom_domain | block for custom domain configuration | `object({})` | `null` | No |
| customer_managed_key | block for custom the configuration of custom keys | `object({})` | `null` | No |
| identity | block for custom the configuration of managed identity  | `object({})` | `null` | No |
| blob_properties | block for custom the configuration of the blobs | `object({})` | `null` | No |
| queue_properties | block for custom the configuration of queue properties | `object({})` | `null` | No |
| static_website | block for custom the configuration of queue static website | `object({})` | `null` | No |
| share_properties | block for custom the configuration of queue share properties | `object({})` | `null` | No |
| network_rules | block for custom the configuration of network rules | `object({})` | `null` | No |
| large_file_share_enabled | Is Large File Share Enabled | `bool` | `null` | No |
| azure_files_authentication | block for custom the configuration of azure files authentication | `object({})` | `null` | No |
| routing | block for custom the configuration of routing | `object({})` | `null` | No |
| queue_encryption_key_type | The encryption type of the queue service | `string` | `Service` | No |
| table_encryption_key_type | The encryption type of the table service. Possible values are Service and Account | `string` | `Service` | No |
| infrastructure_encryption_enabled | Is infrastructure encryption enabled? Changing this forces a new resource to be created | `bool` | `False` | No |
| immutability_policy | block for custom the configuration of immutability policy | `object({})` | `null` | No |
| sas_policy | block for custom the configuration of sas policy | `object({})` | `null` | No |
| allowed_copy_scope | Restrict copy to and from Storage Accounts within an AAD tenant or with Private Links to the same VNet | `string` | `null` | No |
| sftp_enabled | Boolean, enable SFTP for the storage account | `bool` | `False` | No |
| tags | tags for the resource | `map(string)` | `{}` | No |
| azure_ad_groups | list of azure AD groups that will be granted the Application Insights Component Contributor role  | `list` | `[]` | No |
| container | parameters for container creation | `object({})` | `{}` | No |


## Objects and map variables list of acceptable parameters
| Variable Name (Block) | Parameter | Description | Type | Default | Required |
|-----------------------|-----------|-------------|------|---------|:--------:|
| custom_domain | name | The Custom Domain Name to use for the Storage Account, which will be validated by Azure | `string` | `null` | `Yes` |
| custom_domain | use_subdomain | Should the Custom Domain Name be validated by using indirect CNAME validation? | `bool` | `false` | No |
| customer_managed_key | key_vault_key_id | The ID of the Key Vault Key, supplying a version-less key ID will enable auto-rotation of this key | `string` | `null` | `Yes` |
| customer_managed_key | user_assigned_identity_id | The ID of a user assigned identity | `string` | `null` | `Yes` |
| identity | type | Specifies the type of Managed Service Identity that should be configured on this Storage Account | `string` | `null` | `Yes` |
| identity | identity_ids | Specifies a list of User Assigned Managed Identity IDs to be assigned to this Storage Account | `liststring()` | `null` | No |
| blob_properties | cors_rule (allowed_headers) | A list of headers that are allowed to be a part of the cross-origin request | `list(string)` | `null` | `Yes` |
| blob_properties | cors_rule (allowed_methods) | A list of HTTP methods that are allowed to be executed by the origin | `list(string)` | `null` | `Yes` |
| blob_properties | cors_rule (allowed_origins) | A list of origin domains that will be allowed by CORS | `list(string)` | `null` | `Yes` |
| blob_properties | cors_rule (exposed_headers) | A list of response headers that are exposed to CORS clients | `list(string)` | `null` | `Yes` |
| blob_properties | cors_rule (max_age_in_seconds) | The number of seconds the client should cache a preflight response | `number` | `null` | `Yes` |
| blob_properties | delete_retention_policy (days) | Specifies the number of days that the blob should be retained, between 1 and 365 days. Defaults to 7 | `number` | `7` | No |
| blob_properties | restore_policy (days) | Specifies the number of days that the blob can be restored, between 1 and 365 days | `number` | `null` | No |
| blob_properties | versioning_enabled | Is versioning enabled | `bool` | `false` | No |
| blob_properties | change_feed_enabled | Is the blob service properties for change feed events enabled | `bool` | `false` | No |
| blob_properties | change_feed_retention_in_days | The duration of change feed events retention in days | `number` | `0` | No |
| blob_properties | default_service_version | The API Version which should be used by default for requests to the Data Plane API if an incoming request doesn't specify an API Version | `string` | `null` | No |
| blob_properties | last_access_time_enabled | Is the last access time based tracking enabled? | `bool` | `false` | No |
| blob_properties | container_delete_retention_policy (days) | Specifies the number of days that the container should be retained, between 1 and 365 days | `number` | `7` | No |
| queue_properties | cors_rule (allowed_headers) | A list of headers that are allowed to be a part of the cross-origin request | `list(string)` | `null` | `Yes` |
| queue_properties | cors_rule (allowed_methods) | A list of HTTP methods that are allowed to be executed by the origin | `list(string)` | `null` | `Yes` |
| queue_properties | cors_rule (allowed_origins) | A list of origin domains that will be allowed by CORS | `list(string)` | `null` | `Yes` |
| queue_properties | cors_rule (exposed_headers) | A list of response headers that are exposed to CORS clients | `list(string)` | `null` | `Yes` |
| queue_properties | cors_rule (max_age_in_seconds) | The number of seconds the client should cache a preflight response | `number` | `null` | `Yes` |
| queue_properties | logging (delete) | Indicates whether all delete requests should be logged | `bool` | `null` | `Yes` |
| queue_properties | logging (read) | Indicates whether all read requests should be logged | `bool` | `null` | `Yes` |
| queue_properties | logging (version) | The version of storage analytics to configure | `string` | `null` | `Yes` |
| queue_properties | logging (write) | Indicates whether all write requests should be logged | `bool` | `null` | `Yes` |
| queue_properties | logging (retention_policy_days) | Indicates whether all write requests should be logged | `bool` | `null` | No |
| queue_properties | minute_metrics (enabled) | Indicates whether minute metrics are enabled for the Queue service | `bool` | `null` | `Yes` |
| queue_properties | minute_metrics (version) | The version of storage analytics to configure | `string` | `null` | `Yes` |
| queue_properties | minute_metrics (include_apis) | Indicates whether metrics should generate summary statistics for called API operations | `bool` | `null` | No |
| queue_properties | minute_metrics (retention_policy_days) | Specifies the number of days that logs will be retained | `bool` | `null` | No |
| queue_properties | hour_metrics (enabled) | Indicates whether minute metrics are enabled for the Queue service | `bool` | `null` | `Yes` |
| queue_properties | hour_metrics (version) | The version of storage analytics to configure | `string` | `null` | `Yes` |
| queue_properties | hour_metrics (include_apis) | Indicates whether metrics should generate summary statistics for called API operations | `bool` | `null` | No |
| queue_properties | hour_metrics (retention_policy_days) | Specifies the number of days that logs will be retained | `bool` | `null` | No |
| static_website | index_document | the webpage that azure storage serves for requests to the root of a website or any subfolder | `string` | `null` | No |
| static_website | error_404_document | the absolute path to a custom webpage that should be used when a request is made which does not correspond to an existing file | `string` | `null` | No |
| share_properties | cors_rule (allowed_headers) | A list of headers that are allowed to be a part of the cross-origin request | `list(string)` | `null` | `Yes` |
| share_properties | cors_rule (allowed_methods) | A list of HTTP methods that are allowed to be executed by the origin | `list(string)` | `null` | `Yes` |
| share_properties | cors_rule (allowed_origins) | A list of origin domains that will be allowed by CORS | `list(string)` | `null` | `Yes` |
| share_properties | cors_rule (exposed_headers) | A list of response headers that are exposed to CORS clients | `list(string)` | `null` | `Yes` |
| share_properties | cors_rule (max_age_in_seconds) | The number of seconds the client should cache a preflight response | `number` | `null` | `Yes` |
| share_properties | retention_policy (days) | Specifies the number of days that the azurerm_storage_share should be retained, between 1 and 365 days | `number` | `null` | No |
| share_properties | smb (versions) | A set of SMB protocol versions. Possible values are SMB2.1, SMB3.0, and SMB3.1.1 | `string` | `null` | No |
| share_properties | smb (authentication_types) | A set of SMB authentication methods. Possible values are NTLMv2, and Kerberos | `string` | `null` | No |
| share_properties | smb (kerberos_ticket_encryption_type) | A set of Kerberos ticket encryption. Possible values are RC4-HMAC, and AES-256 | `string` | `null` | No |
| share_properties | smb (channel_encryption_type) | A set of SMB channel encryption. Possible values are AES-128-CCM, AES-128-GCM, and AES-256-GCM | `string` | `null` | No |
| share_properties | smb (multichannel_enabled ) | Indicates whether multichannel is enabled. Defaults to false. This is only supported on Premium storage accounts | `bool` | `null` | No |
| network_rules | default_action | Specifies the default action of allow or deny when no other rules match | `string` | `Allow` | `Yes` |
| network_rules | bypass | Specifies whether traffic is bypassed for Logging/Metrics/AzureServicesh | `list(string)` | `[AzureServices]` | No |
| network_rules | ip_rules |List of public IP or IP ranges in CIDR Format. Only IPv4 addresses are allowed | `list(string)` | `[]` | No |
| network_rules | virtual_network_subnet_ids | A list of resource ids for subnets | `list(string)` | `[]` | No |
| network_rules | private_link_access (endpoint_resource_id) | The resource id of the resource access rule to be granted access | `string` | `null` | `Yes` |
| network_rules | private_link_access (endpoint_tenant_id) | The tenant id of the resource of the resource access rule to be granted access | `string` | `null` | No |
| azure_files_authentication | directory_type | Specifies the directory service used. Possible values are AADDS, AD and AADKERB. Mandatory only when directory_type = AD | `string` | `null` | `Yes` |
| azure_files_authentication | active_directory (storage_sid) | Specifies the security identifier (SID) for Azure Storage | `string` | `null` | `Yes` |
| azure_files_authentication | active_directory (domain_name) | Specifies the primary domain that the AD DNS server is authoritative for | `string` | `null` | `Yes` |
| azure_files_authentication | active_directory (domain_sid) | Specifies the security identifier (SID) | `string` | `null` | `Yes` |
| azure_files_authentication | active_directory (domain_guid) | Specifies the domain GUID | `string` | `null` | `Yes` |
| azure_files_authentication | active_directory (forest_name) | Specifies the Active Directory forest | `string` | `null` | `Yes` |
| azure_files_authentication | active_directory (netbios_domain_name) | Specifies the NetBIOS domain name | `string` | `null` | `Yes` |
| routing | publish_internet_endpoints | Should internet routing storage endpoints be published? | `bool` | `null` | No |
| routing | publish_microsoft_endpoints | Should Microsoft routing storage endpoints be published? | `bool` | `null` | No |
| routing | publish_microsoft_endpoints | Specifies the kind of network routing opted by the user. Possible values are InternetRouting and MicrosoftRouting | `string` | `MicrosoftRouting` | No |
| immutability_policy | allow_protected_append_writes | When enabled, new blocks can be written to an append blob while maintaining immutability protection and compliance. Only new blocks can be added and any existing blocks cannot be modified or deleted | `bool` | `null` | No |
| immutability_policy | state | Defines the mode of the policy | `string` | `null` | No |
| immutability_policy | period_since_creation_in_days | The immutability period for the blobs in the container since the policy creation, in days | `number` | `null` | No |
| sas_policy | expiration_period | The SAS expiration period in format of DD.HH:MM:SS | `string` | `null` | `Yes` |
| sas_policy | expiration_action | The SAS expiration action. The only possible value is Log at this moment | `string` | `Log` | No |
| container | name | container name | `string` | `null` | `Yes` |
| container | container_access_type | blob, private etc | `string` | `null` | No |
| containers | ad_group | azure group object id | `string` | `null` | No |

## Output variables

| Name | Description |
|------|-------------|
| id | storage account id |
| name | storage account name |

## Documentation
Terraform Azure Storage Account: <br>
[https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/storage_account](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/storage_account)