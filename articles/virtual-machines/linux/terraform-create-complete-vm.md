---
title: Teljes Linux rendszerű virtuális gép létrehozása az Azure-ban a Terraform használata |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre és kezelhet Linuxos virtuális gép teljes környezet az Azure-ban a Terraform használata
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/14/2017
ms.author: echuvyrov
ms.openlocfilehash: 572cbeec9a259c6996f40696f115218720397227
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56651134"
---
# <a name="create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Hozzon létre egy Linux virtuális gép teljes infrastruktúra a terraform használatával Azure-ban

A Terraform meghatároznunk és létrehoznunk a teljes infrastruktúra-megoldásokat az Azure-ban teszi lehetővé. A Terraform sablonok emberek számára olvasható formátumban, létrehozása és konfigurálása az Azure-erőforrások egységes, reprodukálható módon fejleszt. Ez a cikk bemutatja, hogyan hozhat létre teljes Linux-környezet és a támogató erőforrások a terraform használatával. Azt is megtudhatja, hogyan [telepítse és konfigurálja a Terraform](terraform-install-configure.md).


## <a name="create-azure-connection-and-resource-group"></a>Azure-kapcsolat és az erőforráscsoport létrehozása

Vegyük minden szakasza egy Terraform-sablon használatával. Emellett megtekintheti a teljes verzióját a [Terraform sablon](#complete-terraform-script) másolja és illessze be.

A `provider` szakasz utasítja el a Terraformban az Azure-szolgáltatót használják. Használatával lekérjük az értékeket a *subscription_id*, *client_id*, *titkos ügyfélkódot*, és *tenant_id*, lásd: [telepítése és a Terraform konfigurálása](terraform-install-configure.md). 

> [!TIP]
> Ha az értékek a környezeti változók létrehozásához, vagy használja a [Azure Cloud Shell Bash felület](/azure/cloud-shell/overview) , nem kell tartalmaznia a változódeklarációkat ebben a szakaszban.

```tf
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

A következő szakasz egy nevű erőforráscsoportot hoz létre `myResourceGroup` a a `eastus` helye:

```tf
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags {
        environment = "Terraform Demo"
    }
}
```

További szakaszaiban az erőforráscsoportot, a hivatkozás *${azurerm_resource_group.myterraformgroup.name}*.

## <a name="create-virtual-network"></a>Virtuális hálózat létrehozása
A következő szakasz egy nevű virtuális hálózatot hoz létre *myVnet* a a *10.0.0.0/16* címtér:

```tf
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags {
        environment = "Terraform Demo"
    }
}
```

A következő rész alhálózatot hoz létre nevű *mySubnet* a a *myVnet* virtuális hálózat:

```tf
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Nyilvános IP-cím létrehozása
Erőforrások eléréséhez az interneten keresztül, hozzon létre, és egy nyilvános IP-cím hozzárendelése a virtuális gép. A következő szakaszban létrehoz egy nyilvános IP-címet *myPublicIP*:

```tf
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    allocation_method            = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása
Hálózati biztonsági csoportokkal szabályozhatja a hálózati forgalmat a virtuális gép adataikkal. Az alábbi szakasz nevű hálózati biztonsági csoportot hoz létre *myNetworkSecurityGroup* és a egy szabályt, amely engedélyezi az SSH-forgalmat a 22-es TCP-portot határozza meg:

```tf
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Virtuális hálózati kártya létrehozása
Egy virtuális hálózati kártya (NIC) egy adott virtuális hálózaton, a nyilvános IP-cím és a hálózati biztonsági csoportot a virtuális Géphez csatlakozik. A következő szakasz a Terraform sablonból hoz létre egy virtuális hálózati Adaptert *myNIC* csatlakozik a létrehozott virtuális hálózati erőforrások:

```tf
resource "azurerm_network_interface" "myterraformnic" {
    name                = "myNIC"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    network_security_group_id = "${azurerm_network_security_group.myterraformnsg.id}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-storage-account-for-diagnostics"></a>Diagnosztikai tárfiók létrehozása
A rendszerindítási diagnosztika tárolni a virtuális gép, egy tárfiókra van szükség. Ezek a rendszerindítási diagnosztika segíthet a problémák elhárításában, és figyelheti a virtuális gép állapotát. A storage-fiókot hoz létre, csak az, hogy a rendszerindítási diagnosztikai adatok tárolására. Minden tárfióknak egyedi névvel kell rendelkeznie, mert a következő szakaszban néhány véletlenszerű szöveg hoz létre:

```tf
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}
```

Most egy tárfiókot is létrehozhat. A következő szakaszban az előző lépésben létrehozott véletlenszerű szöveg alapján nevű hoz létre egy storage-fiók:

```tf
resource "azurerm_storage_account" "mystorageaccount" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    location            = "eastus"
    account_replication_type = "LRS"
    account_tier = "Standard"

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Az utolsó lépés, hogy hozzon létre egy virtuális Gépet, és használni létrehozott összes erőforrást. A következő szakasz egy nevű virtuális Gépet hoz létre *myVM* , és csatolja a virtuális hálózati Adaptert *myNIC*. A legújabb *Ubuntu 16.04-LTS* lemezképet használja, és a egy felhasználó neve *azureuser* jelszavas hitelesítés le van tiltva a rendszer létrehozza.

 SSH-kulcs adatainak megtalálható a *ssh_keys* szakaszban. Adja meg az érvényes nyilvános SSH-kulcsot a *key_data* mező.

```tf
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = "${azurerm_resource_group.myterraformgroup.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled     = "true"
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>A Terraform teljes szkript

Ezekben a szakaszokban egyesítik, és tekintse meg a Terraform működés közben, hozzon létre egy fájlt nevű *terraform_azure.tf* , és illessze be az alábbi tartalommal:

```tf
# Configure the Microsoft Azure Provider
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn’t exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    allocation_method            = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = "${azurerm_resource_group.myterraformgroup.name}"
    network_security_group_id = "${azurerm_network_security_group.myterraformnsg.id}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags {
        environment = "Terraform Demo"
    }
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = "${azurerm_resource_group.myterraformgroup.name}"
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = "${azurerm_resource_group.myterraformgroup.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled = "true"
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Hozhat létre, és az infrastruktúra üzembe helyezése
A Terraform sablonnal létrehozott az első lépéseként a Terraform inicializálása. Ez a lépés biztosítja, hogy a Terraform hozhat létre a sablont az Azure-ban minden előfeltétel.

```bash
terraform init
```

A következő lépés, hogy a Terraform áttekintése és jóváhagyása a sablont. Ebben a lépésben összehasonlítja a kért erőforrások Terraform által mentett állapot adatait, és majd megjeleníti a tervezett végrehajtási. Erőforrások *nem* létrehozása az Azure-ban.

```bash
terraform plan
```

Az előző parancs végrehajtása, után kell megjelennie a következő képernyőhöz hasonló:

```bash
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn’t specify an “-out” parameter to save this plan, so when
“apply” is called, Terraform can’t guarantee this is what will execute.
  + azurerm_resource_group.myterraform
      <snip>
  + azurerm_virtual_network.myterraformnetwork
      <snip>
  + azurerm_network_interface.myterraformnic
      <snip>
  + azurerm_network_security_group.myterraformnsg
      <snip>
  + azurerm_public_ip.myterraformpublicip
      <snip>
  + azurerm_subnet.myterraformsubnet
      <snip>
  + azurerm_virtual_machine.myterraformvm
      <snip>
Plan: 7 to add, 0 to change, 0 to destroy.
```

Ha mindent helyesnek tűnik, és készen áll az Azure-infrastruktúra felépítéséhez, alkalmazza a Terraform-sablon:

```bash
terraform apply
```

Miután befejeződött a Terraform, a Virtuálisgép-infrastruktúra készen áll. Szerezze be a virtuális gép nyilvános IP-címét [az vm show](/cli/azure/vm):

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Ezek közül az SSH a virtuális géphez:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>További lépések
Alapvető infrastruktúra a Terraform használatával létrehozott Azure-ban. Az összetettebb esetekhez, beleértve a példákat, amely használható terheléselosztók és a virtuális gép méretezési csoportokat, lásd: számos [az Azure Terraform példák](https://github.com/hashicorp/terraform/tree/master/examples). Támogatott Azure-szolgáltatók naprakész listáját, tekintse meg a [Terraform dokumentáció](https://www.terraform.io/docs/providers/azurerm/index.html).
