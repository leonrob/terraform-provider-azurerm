---
subcategory: "NetApp"
layout: "azurerm"
page_title: "Azure Resource Manager: azurerm_netapp_account"
description: |-
  Manages a NetApp Account.
---

# azurerm_netapp_account

Manages a NetApp Account.

~> **NOTE:** Azure allows only one active directory can be joined to a single subscription at a time for NetApp Account.

## NetApp Account Usage

```hcl
resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

data "azurerm_client_config" "current" {
}

resource "azurerm_user_assigned_identity" "example" {
  name                = "anf-user-assigned-identity"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
}

resource "azurerm_netapp_account" "example" {
  name                = "netappaccount"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name

  active_directory {
    username            = "aduser"
    password            = "aduserpwd"
    smb_server_name     = "SMBSERVER"
    dns_servers         = ["1.2.3.4"]
    domain              = "westcentralus.com"
    organizational_unit = "OU=FirstLevel"
  }

  identity {
    type = "UserAssigned"
    identity_ids = [
      azurerm_user_assigned_identity.example.id
    ]
  }
}

```

## Argument Reference

The following arguments are supported:

* `name` - (Required) The name of the NetApp Account. Changing this forces a new resource to be created.

* `resource_group_name` - (Required) The name of the resource group where the NetApp Account should be created. Changing this forces a new resource to be created.

* `location` - (Required) Specifies the supported Azure location where the resource exists. Changing this forces a new resource to be created.

* `active_directory` - (Optional) A `active_directory` block as defined below.

* `identity` - (Optional) The identity block where it is used when customer managed keys based encryption will be enabled.

* `tags` - (Optional) A mapping of tags to assign to the resource.

---

The `active_directory` block supports the following:

* `dns_servers` - (Required) A list of DNS server IP addresses for the Active Directory domain. Only allows `IPv4` address.

* `domain` - (Required) The name of the Active Directory domain.

* `smb_server_name` - (Required) The NetBIOS name which should be used for the NetApp SMB Server, which will be registered as a computer account in the AD and used to mount volumes.

* `username` - (Required) The Username of Active Directory Domain Administrator.

* `password` - (Required) The password associated with the `username`.

* `organizational_unit` - (Optional) The Organizational Unit (OU) within the Active Directory Domain.

---

---
The `identity` block supports the following:

* `type` - (Required) The identity type, which can be `SystemAssigned` or `UserAssigned`. Only one type at a time is supported by Azure NetApp Files.
* `identity_ids` - (Optional) The identity id of the user assigned identity to use when type is `UserAssigned`
---

~> **IMPORTANT:** Changing identity type from `SystemAssigned` to `UserAssigned` is a supported operation but the reverse is not supported from within Terraform Azure NetApp Files module. 

## Attributes Reference

In addition to the Arguments listed above - the following Attributes are exported:

* `id` - The ID of the NetApp Account.

## Timeouts

The `timeouts` block allows you to specify [timeouts](https://www.terraform.io/language/resources/syntax#operation-timeouts) for certain actions:

* `create` - (Defaults to 30 minutes) Used when creating the NetApp Account.
* `update` - (Defaults to 30 minutes) Used when updating the NetApp Account.
* `read` - (Defaults to 5 minutes) Used when retrieving the NetApp Account.
* `delete` - (Defaults to 30 minutes) Used when deleting the NetApp Account.

## Import

NetApp Accounts can be imported using the `resource id`, e.g.

```shell
terraform import azurerm_netapp_account.example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/group1/providers/Microsoft.NetApp/netAppAccounts/account1
```
