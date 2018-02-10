---
title: "Terraform használatával teljes Linux virtuális gép létrehozása az Azure-ban |} Microsoft Docs"
description: "Terraform használata létrehozását és kezelését a teljes Linux virtuálisgép-környezetet az Azure-ban"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: timlt
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/14/2017
ms.author: echuvyrov
ms.openlocfilehash: 8abc98a6f1a222e2533eb9f742fb83f4a23d5e90
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Hozzon létre egy Linux virtuális gép teljes infrastruktúra az Azure-ban Terraform

Terraform segítségével határozza meg, és a teljes infrastruktúra központi telepítések létrehozását az Azure-ban. Terraform sablonok emberek számára olvasható formátumban létrehozása és konfigurálása az Azure-erőforrások következetes és reprodukálható módon hoz létre. Ez a cikk bemutatja, hogyan hozzon létre egy teljes körű Linux környezetet és az azt támogató Terraform erőforrásokhoz. Azt is megtudhatja hogyan [telepítése és konfigurálása Terraform](terraform-install-configure.md).


## <a name="create-azure-connection-and-resource-group"></a>Az Azure-kapcsolat és az erőforráscsoport létrehozása

Ugorjunk, minden szakasza egy Terraform sablon használatával. Azt is láthatja, a teljes verzióját a [Terraform sablon](#complete-terraform-script) másolja és illessze be.

A `provider` szakasz közli Terraform egy Azure-szolgáltató használatára. Értékek beolvasandó *ELŐFIZETÉS_AZONOSÍTÓJA*, *client_id*, *client_secret*, és *tenant_id*, lásd: [telepítése és Konfigurálja a Terraform](terraform-install-configure.md). 

> [!TIP]
> Ha az értékek a környezeti változók létrehozása, vagy használja a [Azure Cloud rendszerhéj Bash élmény](/azure/cloud-shell/overview) , nem kell ahhoz, hogy ez a szakasz a változók deklarációja szerepeljen.

```tf
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

A következő szakasz hoz létre egy erőforráscsoportot `myResourceGroup` a a `eastus` helye:

```tf
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "East US"

    tags {
        environment = "Terraform Demo"
    }
}
```

A kiegészítő részekben hivatkozik az erőforráscsoport *${azurerm_resource_group.myterraformgroup.name}*.

## <a name="create-virtual-network"></a>Virtuális hálózat létrehozása
A következő szakasz létrehoz egy virtuális hálózatot nevű *myVnet* a a *10.0.0.0/16* címtér:

```tf
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "East US"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags {
        environment = "Terraform Demo"
    }
}
```
: A következő szakasz nevű alhálózatot hoz létre *mySubnet* a a *myVnet* virtuális hálózat

```tf
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Nyilvános IP-cím létrehozása
Internetes erőforrások eléréséhez hozzon létre, és egy nyilvános IP-címet rendel a virtuális Gépet. A következő szakasz hoz létre egy nyilvános IP-cím nevű *myPublicIP*:

```tf
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "East US"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása
Hálózati biztonsági csoportok szabályozhatja a hálózati forgalmat a virtuális Gépet mindkét. A következő szakasz nevű hálózati biztonsági csoportot hoz létre *myNetworkSecurityGroup* és a szabály az SSH-forgalmat engedélyezi a 22-es TCP-portot határozza meg:

```tf
resource "azurerm_network_security_group" "temyterraformpublicipnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "East US"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    ;
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


## <a name="create-virtual-network-interface-card"></a>Hozzon létre a virtuális hálózati kártya
A virtuális hálózati kártya (NIC) a virtuális gép csatlakozik egy adott virtuális hálózaton, a nyilvános IP-cím és a hálózati biztonsági csoport. A következő szakasz Terraform sablonban hoz létre a virtuális hálózati adapter nevű *myNIC* csatlakozik a létrehozott virtuális hálózati erőforrások:

```tf
resource "azurerm_network_interface" "myterraformnic" {
    name                = "myNIC"
    location            = "East US"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

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
Rendszerindítási diagnosztika tárolásához a virtuális gépek, a tárfiók van szükség. A rendszerindítási diagnosztika segíthetnek a problémák megoldásához és a virtuális gép állapotának figyelése. A storage-fiók létrehozásakor a rendszer csak a rendszerindítási diagnosztika adatainak tárolására. Minden tárfiók egyedi névvel kell rendelkeznie, mint a következő szakaszban néhány véletlenszerű szöveg állít elő:

```tf
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}
```

Most egy tárfiókot is létrehozhat. Az alábbi szakasz a tárfiók a nevét, az előző lépésben létrehozott véletlenszerű szöveg alapján hoz létre:

```tf
resource "azurerm_storage_account" "mystorageaccount" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    location            = "East US"
    account_replication_type = "LRS"
    account_tier = "Standard"

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Az utolsó lépés a virtuális gép létrehozása és használata a létrehozott összes erőforrást. A következő szakasz hoz létre egy elnevezett VM *myVM* , és csatolja a virtuális hálózati adapter nevű *myNIC*. A legújabb *Ubuntu 16.04-es lts verzió* lemezképet használja, és a felhasználó nevű *azureuser* jön létre a jelszó-hitelesítés le van tiltva.

 SSH-kulcs adatok találhatók a *ssh_keys* szakasz. Adja meg az érvényes nyilvános SSH-kulcs a *key_data* mező.

```tf
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "East US"
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

## <a name="complete-terraform-script"></a>Teljes Terraform parancsfájl

Ezek a szakaszok egyesítik és Terraform működés közben megtekintéséhez nevű fájl létrehozása *terraform_azure.tf* , majd illessze be a következőket:

```tf
variable "resourcename" {
  default = "myResourceGroup"
}

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
    location = "East US"

    tags {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "East US"
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
    location                     = "East US"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "East US"
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
    location                  = "East US"
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
    location                    = "East US"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "East US"
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


## <a name="build-and-deploy-the-infrastructure"></a>Hozza létre, és az infrastruktúra központi telepítéséhez
A létrehozott Terraform sablont az első lépés Terraform inicializálni. Ez a lépés biztosítja, hogy Terraform rendelkezik-e a szükséges előfeltételek maradéktalanul az Azure-sablon létrehozásához.

```bash
terraform init
```

A következő lépésre Terraform tekintse át, és ellenőrizze a sablont. Ez a lépés összehasonlítja a kért erőforrások Terraform által mentett állapot információkhoz, és majd exportálja a tervezett végrehajtása. Erőforrások *nem* létrehozása az Azure-ban.

```bash
terraform plan
```

Az előző parancs végrehajtása után kell megjelennie a következő képernyő hasonlót:

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

Ha minden megvizsgálja a helyes-e, és készen áll az Azure-infrastruktúra felépítéséhez alkalmazni a sablon Terraform:

```bash
terraform apply
```

Miután Terraform befejeződött, a virtuális gép infrastruktúra készen áll. A nyilvános IP-címet a virtuális gép az beszerzése [az vm megjelenítése](/cli/azure/vm#az_vm_show):

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Akkor majd SSH, a virtuális Gépet:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>További lépések
Alapvető infrastruktúra az Azure-ban hozott létre a Terraform használatával. Összetettebb forgatókönyveket, beleértve a példák, amelyek használatát egy terheléselosztó és a virtuális gép skálázása beállítása, lásd: számos [Terraform példák az Azure-](https://github.com/hashicorp/terraform/tree/master/examples). Támogatott Azure szolgáltatók naprakész listáját, tekintse meg a [Terraform dokumentáció](https://www.terraform.io/docs/providers/azurerm/index.html).
