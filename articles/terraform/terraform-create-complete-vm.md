---
title: Rövid útmutató – A Terraform segítségével hozzon létre egy teljes Linux virtuális gép az Azure-ban
description: Ebben a rövid útmutatóban a Terraform segítségével hozhat létre és kezelhet egy teljes Linux virtuálisgép-környezetet az Azure-ban
keywords: azure devops terraform linux vm virtuális gép
ms.topic: quickstart
ms.date: 03/15/2020
ms.openlocfilehash: f262734cc16d97e4d73af371410403a4cbb8815e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79415461"
---
# <a name="quickstart-create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Rövid útmutató: Teljes Linux virtuálisgép-infrastruktúra létrehozása az Azure-ban a Terraform segítségével

A Terraform lehetővé teszi a teljes infrastruktúra-telepítések definiálását és létrehozását az Azure-ban. Terraform-sablonokat emberileg olvasható formátumban hozhat létre, amelyek egységes, reprodukálható módon hozzák létre és konfigurálják az Azure-erőforrásokat. Ez a cikk bemutatja, hogyan hozhat létre egy teljes Linux-környezetet és támogató erőforrásokat a Terraform segítségével. Azt is megtudhatja, hogyan kell [telepíteni és konfigurálni Terraform](terraform-install-configure.md).

> [!NOTE]
> A Terraform specifikus támogatás, kérjük, forduljon terraform közvetlenül az egyik közösségi csatornák:
>
>    * A közösségi portál [Terraform szakasza](https://discuss.hashicorp.com/c/terraform-core) kérdéseket, használati eseteket és hasznos mintákat tartalmaz.
>
>    * A szolgáltatóval kapcsolatos kérdéseivel kérjük, látogasson el a közösségi portál [Terraform Szolgáltatók](https://discuss.hashicorp.com/c/terraform-providers) részébe.


## <a name="create-azure-connection-and-resource-group"></a>Azure-kapcsolat és erőforráscsoport létrehozása

Nézzük át a Terraform sablon minden egyes szakaszát. Megtekintheti a [Terraform sablon](#complete-terraform-script) teljes verzióját is, amelyet másolhat és beilleszthet.

A `provider` szakasz megmondja a Terraformnak, hogy használjon Egy Azure-szolgáltatót. A `subscription_id`, `client_id`és `client_secret`a `tenant_id`, értékét a Terraform telepítése és konfigurálása című témakörben [tetszése idot.](terraform-install-configure.md) 

> [!TIP]
> Ha környezeti változókat hoz létre az értékekhez, vagy az [Azure Cloud Shell Bash-élményt](/azure/cloud-shell/overview) használja, ebben a szakaszban nem kell megadnia a változódeklarációkat.

```hcl
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you're using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
    
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

A következő szakasz létrehoz `myResourceGroup` egy `eastus` erőforráscsoportot, amelyet a hely nevez meg:

```hcl
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

További szakaszokban az erőforráscsoportra `${azurerm_resource_group.myterraformgroup.name}`hivatkozik.

## <a name="create-virtual-network"></a>Virtuális hálózat létrehozása
A következő szakasz létrehoz `myVnet` egy `10.0.0.0/16` virtuális hálózatot a címtérben:

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

A következő szakasz létrehoz `mySubnet` egy `myVnet` alhálózatot a virtuális hálózatban:

```hcl
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Nyilvános IP-cím létrehozása
Erőforrások eléréséhez az interneten keresztül, hozzon létre és rendeljen hozzá egy nyilvános IP-címet a virtuális géphez. A következő szakasz létrehoz egy `myPublicIP`nyilvános IP-címet, amelynek neve:

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
A hálózati biztonsági csoportok szabályozzák a hálózati forgalom áramlását a virtuális gépbe és a virtuális gépből. A következő szakasz létrehoz egy `myNetworkSecurityGroup` hálózati biztonsági csoport nevű, és meghatározza a szabályt, amely lehetővé teszi az SSH-forgalom A TCP port 22:

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


## <a name="create-virtual-network-interface-card"></a>Virtuális hálózati illesztőkártya létrehozása
A virtuális hálózati kártya (NIC) csatlakozik a virtuális gép egy adott virtuális hálózat, nyilvános IP-cím és a hálózati biztonsági csoport. A Terraform sablon következő szakasza létrehoz `myNIC` egy virtuális hálózati adaptert, amely a létrehozott virtuális hálózati erőforrásokhoz csatlakozik:

```hcl
resource "azurerm_network_interface" "myterraformnic" {
    name                        = "myNIC"
    location                    = "eastus"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name

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

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
}
```


## <a name="create-storage-account-for-diagnostics"></a>Tárfiók létrehozása diagnosztikához
A virtuális gép rendszerindítási diagnosztikájának tárolásához szüksége van egy tárfiókra. Ezek a rendszerindítási diagnosztika segítségével elháríthatja a problémákat, és figyelemmel kíséri a virtuális gép állapotát. A létrehozott tárfiók csak a rendszerindítási diagnosztikai adatok tárolására szolgál. Mivel minden tárfióknak egyedi névvel kell rendelkeznie, a következő szakasz véletlenszerű szöveget hoz létre:

```hcl
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}
```

Most létrehozhat egy tárfiókot. A következő szakasz létrehoz egy tárfiókot, az előző lépésben létrehozott véletlenszerű szöveg alapján:

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

Az utolsó lépés egy virtuális gép létrehozása, és az összes létrehozott erőforrás használata. A következő szakasz létrehoz `myVM` egy virtuális gép nevű, és csatolja a virtuális hálózati adapter nevű. `myNIC` A `Ubuntu 16.04-LTS` rendszer a legújabb lemezképet `azureuser` használja, és egy megnevezett felhasználót hoz létre, és a jelszó-hitelesítés le van tiltva.

 Az SSH kulcsadatai `ssh_keys` a szakaszban találhatóak. Adjon meg egy nyilvános `key_data` SSH kulcsot a terepen.

```hcl
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = file("/home/azureuser/.ssh/authorized_keys")
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Terraform parancsfájl teljes befejezése

Ha össze szeretné hozni ezeket a szakaszokat, és `terraform_azure.tf` látni szeretné a Terraform működés közben című részét, hozzon létre egy nevű fájlt, és illessze be a következő tartalmat:

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you're using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}

    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn't exist
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

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
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
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = file("/home/azureuser/.ssh/authorized_keys")
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Az infrastruktúra kiépítése és üzembe helyezése
A Terraform sablon létrehozásával az első lépés a Terraform inicializálása. Ez a lépés biztosítja, hogy a Terraform minden előfeltételt az Azure-ban a sablon létrehozásához.

```bash
terraform init
```

A következő lépés a Terraform felülvizsgálata és a sablon ellenőrzése. Ez a lépés összehasonlítja a kért erőforrásokat a Terraform által mentett állapotadatokkal, majd kiadja a tervezett végrehajtást. Az Azure-erőforrások nem jönnek létre ezen a ponton.

```bash
terraform plan
```

Az előző parancs végrehajtása után a következő höz hasonló képernyőt kell látnia:

```console
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn't specify an "-out" parameter to save this plan, so when
"apply" is called, Terraform can't guarantee this is what will execute.
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

Ha minden helyesnek tűnik, és készen áll az azure-beli infrastruktúra kiépítésére, alkalmazza a sablont a Terraform alkalmazásban:

```bash
terraform apply
```

A Terraform befejeződése után a virtuális gép infrastruktúrája készen áll. Szerezze be a virtuális gép nyilvános IP-címét [az vm](/cli/azure/vm)show-val:

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Ezután SSH a virtuális gép:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [További információ a Terraform azure-beli használatáról](/azure/terraform)