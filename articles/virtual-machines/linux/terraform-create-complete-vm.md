---
title: Teljes linuxos virtuális gép létrehozása az Azure-ban a Terraform használatával
description: Ismerje meg, hogyan hozhat létre és kezelhet teljes linuxos virtuálisgép-környezetet az Azure-ban a Terraform használatával
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tomarchermsft
manager: gwallace
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2019
ms.author: tarcher
ms.openlocfilehash: 819aeb225c4f55f803a5fad19eff33bd1748bf46
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75368929"
---
# <a name="create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Teljes linuxos virtuális gépi infrastruktúra létrehozása az Azure-ban a Terraform

A Terraform lehetővé teszi, hogy az Azure-ban teljes infrastruktúra-telepítéseket határozzon meg és hozzon létre. Az Azure-erőforrások egységes, reprodukálható módon történő létrehozásához és konfigurálásához az Terraform-sablonokat egy ember által olvasható formátumban kell létrehoznia. Ebből a cikkből megtudhatja, hogyan hozhat létre teljes linuxos környezetet és támogató erőforrásokat a Terraform. Azt is megtudhatja, hogyan [telepítheti és konfigurálhatja a Terraform](terraform-install-configure.md).

> [!NOTE]
> A Terraform-specifikus támogatáshoz forduljon közvetlenül a Terraform a közösségi csatornák egyikének használatával:
>
>   • A közösségi portál [Terraform szakasza](https://discuss.hashicorp.com/c/terraform-core) kérdéseket, használati eseteket és hasznos mintákat tartalmaz.
>
>   • A szolgáltatóval kapcsolatos kérdésekért látogasson el a közösségi portál [Terraform-szolgáltatók](https://discuss.hashicorp.com/c/terraform-providers) szakaszára.


## <a name="create-azure-connection-and-resource-group"></a>Azure-beli kapcsolatok és erőforráscsoport létrehozása

Nézzük végig a Terraform-sablonok egyes szakaszait. Megtekintheti a másolható és beilleszthető [Terraform-sablon](#complete-terraform-script) teljes verzióját is.

A `provider` szakasz azt jelzi, hogy a Terraform Azure-szolgáltatót használ. *Subscription_id*, *client_id*, *client_secret*és *tenant_id*értékének beolvasásához tekintse meg a [Terraform telepítése és konfigurálása](terraform-install-configure.md)című témakört. 

> [!TIP]
> Ha környezeti változókat hoz létre az értékekhez, vagy az [Azure Cloud SHELL bash-élményt](/azure/cloud-shell/overview) használja, akkor ebben a szakaszban nem kell tartalmaznia a változó deklarációit.

```hcl
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

A következő szakasz egy `myResourceGroup` nevű erőforráscsoportot hoz létre a `eastus` helyen:

```hcl
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

A további fejezetekben a (z) *$ {azurerm_resource_group. myterraformgroup. name}* erőforrás-csoportra hivatkozik.

## <a name="create-virtual-network"></a>Virtuális hálózat létrehozása
A következő szakasz létrehoz egy *myVnet* nevű virtuális hálózatot a *10.0.0.0/16* címtartomány:

```hcl
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}
```

A következő szakasz létrehoz egy *mySubnet* nevű alhálózatot a *myVnet* virtuális hálózatban:

```hcl
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Nyilvános IP-cím létrehozása
Az erőforrások interneten keresztüli eléréséhez hozzon létre és rendeljen hozzá egy nyilvános IP-címet a virtuális géphez. A következő szakasz egy *myPublicIP*nevű nyilvános IP-címet hoz létre:

```hcl
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása
A hálózati biztonsági csoportok vezérlik a virtuális gépen belüli és kívüli hálózati forgalom folyamatát. A következő szakasz létrehoz egy *myNetworkSecurityGroup* nevű hálózati biztonsági csoportot, és meghatároz egy szabályt, amely engedélyezi az SSH-forgalmat a 22-es TCP-porton:

```hcl
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
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

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Virtuális hálózati kártya létrehozása
A virtuális hálózati kártya (NIC) összekapcsolja a virtuális GÉPET egy adott virtuális hálózattal, nyilvános IP-címmel és hálózati biztonsági csoporttal. A Terraform-sablon következő szakasza egy *myNIC* nevű virtuális hálózati adaptert hoz létre, amely a létrehozott virtuális hálózati erőforrásokhoz csatlakozik:

```hcl
resource "azurerm_network_interface" "myterraformnic" {
    name                        = "myNIC"
    location                    = "eastus"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    network_security_group_id   = azurerm_network_security_group.myterraformnsg.id

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-storage-account-for-diagnostics"></a>Storage-fiók létrehozása a diagnosztika számára
Egy virtuális gép rendszerindítási diagnosztika tárolásához egy Storage-fiókra van szükség. A rendszerindítási diagnosztika segítségével elháríthatja a problémákat, és figyelheti a virtuális gép állapotát. A létrehozott Storage-fiók csak a rendszerindítási diagnosztikai adatait tárolja. Mivel minden egyes Storage-fióknak egyedi névvel kell rendelkeznie, a következő szakasz egy véletlenszerű szöveget hoz létre:

```hcl
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}
```

Most már létrehozhat egy Storage-fiókot. A következő szakasz egy Storage-fiókot hoz létre, amelynek a neve az előző lépésben létrehozott véletlenszerű szöveg alapján történik:

```hcl
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_replication_type    = "LRS"
    account_tier                = "Standard"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Utolsó lépésként hozzon létre egy virtuális gépet, és használja az összes létrehozott erőforrást. Az alábbi szakasz létrehoz egy *myVM* nevű virtuális gépet, és csatolja a *myNIC*nevű virtuális hálózati adaptert. A legújabb *Ubuntu 16,04-LTS* rendszerképet használja a rendszer, és az *azureuser* nevű felhasználó jelszavas hitelesítéssel lett letiltva.

 Az SSH-kulcsokra vonatkozó információk a *ssh_keys* szakaszban találhatók meg. Adjon meg egy érvényes nyilvános SSH-kulcsot a *key_data* mezőben.

```hcl
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
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
        storage_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Terraform-szkript befejezése

Ha az összes szakaszt együtt szeretné megjeleníteni, és a Terraform működés közben látja, hozzon létre egy *terraform_azure. tf* nevű fájlt, és illessze be az alábbi tartalmat:

```hcl
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

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
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

    tags = {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = azurerm_resource_group.myterraformgroup.name
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = azurerm_subnet.myterraformsubnet.id
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = azurerm_public_ip.myterraformpublicip.id
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
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
        storage_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Az infrastruktúra kiépítése és üzembe helyezése
Ha létrehozta a Terraform-sablont, az első lépés a Terraform inicializálása. Ez a lépés biztosítja, hogy a Terraform az összes előfeltételt felkészítse a sablon létrehozásához az Azure-ban.

```bash
terraform init
```

A következő lépés a sablon Terraform áttekintése és érvényesítése. Ez a lépés összehasonlítja a kért erőforrásokat a Terraform által mentett állapotadatok között, majd a tervezett végrehajtást adja vissza. Az erőforrások *nem* az Azure-ban jönnek létre.

```bash
terraform plan
```

Az előző parancs végrehajtása után a következő képernyőhöz hasonlóan kell megjelennie:

```console
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

Ha minden helyesnek tűnik, és készen áll az infrastruktúra létrehozására az Azure-ban, alkalmazza a sablont a Terraform-ben:

```bash
terraform apply
```

A Terraform befejezése után a virtuális gép infrastruktúrája készen áll. Szerezze be a virtuális gép nyilvános IP-címét az [az VM show](/cli/azure/vm)paranccsal:

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Ezután SSH-t használhat a virtuális géphez:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Következő lépések
Alapszintű infrastruktúrát hozott létre az Azure-ban a Terraform használatával. Összetettebb forgatókönyvek esetén, beleértve a terheléselosztó és a virtuálisgép-méretezési csoportok használatát is, [Az Azure számos Terraform-példáját](https://github.com/hashicorp/terraform/tree/master/examples)láthatja. A támogatott Azure-szolgáltatók naprakész listája a [Terraform dokumentációjában](https://www.terraform.io/docs/providers/azurerm/index.html)található.
