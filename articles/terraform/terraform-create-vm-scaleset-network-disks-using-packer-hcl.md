---
title: Oktatóanyag – Hozzon létre egy Azure virtuálisgép-méretezési készletet egy Packer egyéni lemezképből a Terraform használatával
description: A Terraform használatával a Packer által létrehozott egyéni rendszerképből konfigurálhatja és verziószámozhatja az Azure-beli virtuális gépek méretezési csoportját (virtuális hálózattal és felügyelt csatolt lemezekkel együtt).
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 92a8221d625f8b6b73343f74b85fdfcf5e578b23
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472204"
---
# <a name="tutorial-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image-by-using-terraform"></a>Oktatóanyag: Hozzon létre egy Azure virtuálisgép-méretezési készletet egy Packer egyéni lemezképből a Terraform használatával

Ebben az oktatóanyagban a [Terraform](https://www.terraform.io/) segítségével hozzon létre és telepítsen egy [Azure virtuálisgép-méretezési készletet,](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) amelyet egy egyéni lemezképpel hoz létre a [Packer](https://www.packer.io/intro/index.html) használatával a [HashiCorp konfigurációs nyelvet](https://www.terraform.io/docs/configuration/syntax.html) (HCL) használó felügyelt lemezekkel. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be a Terraform telepítését.
> * Változók és kimenetek használata a Terraform üzembe helyezéséhez.
> * Hozzon létre és telepítsen egy hálózati infrastruktúrát.
> * Hozzon létre egy egyéni virtuálisgép-lemezképet a Packer használatával.
> * Hozzon létre és telepítsen egy virtuálisgép-méretezési készletet az egyéni lemezkép használatával.
> * Jumpbox létrehozása és üzembe helyezése.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

- **Terraform**: [Telepítse a Terraformot, és konfigurálja az Azure-hoz való hozzáférést.](terraform-install-configure.md)
- **SSH kulcspár:** [SSH kulcspár létrehozása.](/azure/virtual-machines/linux/mac-create-ssh-keys)
- **Packer**: [A Csomagoló telepítése](https://www.packer.io/docs/install/index.html).

## <a name="create-the-file-structure"></a>A fájlstruktúra létrehozása

Hozzon létre három új fájlt egy üres könyvtárban a következő nevekkel:

- `variables.tf`: Ez a fájl a sablonban használt változók értékeit tartalmazza.
- `output.tf`: Ez a fájl azokat a beállításokat ismerteti, amelyek a telepítés után jelennek meg.
- `vmss.tf`: Ez a fájl a telepíti az infrastruktúra kódját tartalmazza.

##  <a name="create-the-variables"></a>A változók létrehozása 

Ebben a lépésben a Terraform által létrehozott erőforrások beállítására szolgáló változókat adhatja meg.

Szerkesztheti `variables.tf` a fájlt, másolja a következő kódot, majd mentse a módosításokat.

```hcl
variable "location" {
  description = "The location where resources are created"
  default     = "East US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources are created"
  default     = ""
}

```

> [!NOTE]
> A resource_group_name változó alapértelmezett értéke nincs beállítva. Határozza meg saját értékét.

Mentse a fájlt.

A Terraform-sablon telepítésekor meg szeretné szerezni az alkalmazás eléréséhez használt teljesen minősített tartománynevet. A Terraform `output` erőforrástípusát használja, és kérje le az erőforrás `fqdn` tulajdonságát. 

Szerkessze az `output.tf` fájlt, és másolja ki az alábbi kódot, hogy megkapja a virtuális gépek teljes tartománynevét. 

```hcl 
output "vmss_public_ip" {
    value = azurerm_public_ip.vmss.fqdn
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>A hálózati infrastruktúra meghatározása egy sablonban 

Ebben a lépésben az alábbi hálózati infrastruktúrát hozza létre egy új Azure-erőforráscsoportban: 
  - Egy virtuális hálózat 10.0.0.0/16 címtérrel.
  - Egy 10.0.2.0/24 címtérrel rendelkező alhálózat.
  - Két nyilvános IP-cím. Az egyik a virtuális gép méretezési készlet terheléselosztója által használt. A másik az SSH jumpboxhoz való csatlakozásra szolgál.

Egy erőforráscsoportra is szüksége van, ahol az összes erőforrás létrejön. 

Szerkessze és másolja be az alábbi kódot a `vmss.tf` fájlba: 

```hcl

resource "azurerm_resource_group" "vmss" {
  name     = var.resource_group_name
  location = var.location

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = azurerm_resource_group.vmss.name
  virtual_network_name = azurerm_virtual_network.vmss.name
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = var.location
  resource_group_name          = azurerm_resource_group.vmss.name
  allocation_method            = "static"
  domain_name_label            = azurerm_resource_group.vmss.name

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> Címkézze fel az Azure-ban üzembe helyezett erőforrásokat, hogy a jövőben megkönnyítsék azok azonosítását.

## <a name="create-the-network-infrastructure"></a>A hálózati infrastruktúra létrehozása

A Terraform-környezet inicializálásához futtassa a következő parancsot a `.tf` fájlok létrehozásához használt könyvtárban:

```bash
terraform init 
```
 
A szolgáltató beépülő moduljai a Terraform rendszerleíró adatbázisból a `.terraform` parancsot tartalmazó könyvtár mappájába töltődnek le.

Futtassa az alábbi parancsot az infrastruktúra az Azure-ban történő üzembe helyezéséhez.

```bash
terraform apply
```

Győződjön meg arról, hogy a nyilvános IP-cím teljes tartományneve megfeleljen a konfigurációnak.

![Virtuálisgép-méretezéskészlet Terraform teljesen minősített tartománynév nyilvános IP-címhez](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

Az erőforráscsoport a következő erőforrásokat tartalmazza:

![Virtuálisgép-méretezési csoport hálózati Terraform-erőforrásai](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-by-using-packer"></a>Azure-lemezkép létrehozása a Packer használatával
Hozzon létre egy egyéni Linux-lemezképet az oktatóanyag lépéseit [követve Hogyan használhatja a Packert Linux virtuálisgép-lemezképek létrehozásához az Azure-ban.](/azure/virtual-machines/linux/build-image-with-packer)
 
Kövesse a bemutató, hogy hozzon létre egy deprovisioned Ubuntu képet Nginx telepítve.

![A Packer-kép létrehozása után van egy képe](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Az oktatóanyag alkalmazásában a Packer-lemezképben egy parancs fut a Nginx telepítéséhez. A létrehozás alatt saját szkriptet is futtathat.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Az infrastruktúra szerkesztése a virtuálisgép-méretezési csoport hozzáadásához

Ebben a lépésben a következő erőforrásokat hozza létre a korábban üzembe helyezett hálózaton:
- Az alkalmazás kiszolgálásához egy Azure-terheléselosztó. Csatolja a korábban üzembe helyezett nyilvános IP-címhez.
- Egy Azure-terheléselosztó és az alkalmazás kiszolgálására szolgáló szabályok. Csatolja a korábban konfigurált nyilvános IP-címhez.
- Egy Azure-beli háttércímkészlet. Rendelje hozzá a terheléselosztóhoz.
- Az alkalmazás által használt és a terheléselosztón konfigurált állapotmintaport.
- A virtuális gép méretezési készlet, amely a terheléselosztó mögött helyezkedik el, és fut a korábban üzembe helyezett virtuális hálózaton.
- [Nginx](https://nginx.org/) a virtuális gép méretezési csomópontjain telepített egyéni lemezkép.


Adja hozzá az alábbi kódot a `vmss.tf` fájl végéhez.

```hcl

resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = azurerm_public_ip.vmss.id
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = azurerm_resource_group.vmss.name
  loadbalancer_id     = azurerm_lb.vmss.id
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = azurerm_resource_group.vmss.name
  loadbalancer_id     = azurerm_lb.vmss.id
  name                = "ssh-running-probe"
  port                = var.application_port
}

resource "azurerm_lb_rule" "lbnatrule" {
  resource_group_name            = azurerm_resource_group.vmss.name
  loadbalancer_id                = azurerm_lb.vmss.id
  name                           = "http"
  protocol                       = "Tcp"
  frontend_port                  = var.application_port
  backend_port                   = var.application_port
  backend_address_pool_id        = azurerm_lb_backend_address_pool.bpepool.id
  frontend_ip_configuration_name = "PublicIPAddress"
  probe_id                       = azurerm_lb_probe.vmss.id
}

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = data.azurerm_resource_group.image.name
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    id=data.azurerm_image.image.id
  }

  storage_profile_os_disk {
    name              = ""
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_profile_data_disk {
    lun          = 0
    caching        = "ReadWrite"
    create_option  = "Empty"
    disk_size_gb   = 10
  }

  os_profile {
    computer_name_prefix = "vmlab"
    admin_username       = "azureuser"
    admin_password       = "Passwword1234"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = file("~/.ssh/id_rsa.pub")
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = azurerm_subnet.vmss.id
      load_balancer_backend_address_pool_ids = [azurerm_lb_backend_address_pool.bpepool.id]
      primary = true
    }
  }
  
  tags {
    environment = "codelab"
  }
}

```

A következő kód a `variables.tf` fájlhoz adásával testreszabhatja az üzemelő példányt:

```hcl
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>A virtuálisgép-méretezési csoport üzembe helyezése az Azure-ban

Futtassa a következő parancsot a virtuálisgép-méretezési csoport üzembe helyezésének megjelenítéséhez:

```bash
terraform plan
```

A parancs kimenete a következő képhez hasonlóan néz ki:

![A Terraform virtuálisgép-méretezési csoportjának hozzáadási terve](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-plan.png)

Helyezze üzembe a további erőforrásokat az Azure-ban: 

```bash
terraform apply 
```

Az erőforráscsoport tartalma a következő képhez hasonlóan néz ki:

![A Terraform virtuálisgép-méretezési csoportjának erőforráscsoportja](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

Nyisson meg egy böngészőt, és csatlakozzon a parancs által visszaadott teljes tartománynévhez. 


## <a name="add-a-jumpbox-to-the-existing-network"></a>Jumpbox hozzáadása a meglévő hálózathoz 

Ez a választható lépés SSH-hozzáférést tesz lehetővé a virtuálisgép-méretezési csoport példányaihoz egy jumpbox használatával.

Adja hozzá a következő erőforrásokat a meglévő üzemelő példányhoz:
- A virtuálisgép méretezési készletével azonos alhálózathoz csatlakoztatott hálózati adapter
- Ezzel a hálózati adapterrel rendelkező virtuális gép

Adja hozzá az alábbi kódot a `vmss.tf` fájl végéhez:

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = var.location
  resource_group_name          = azurerm_resource_group.vmss.name
  allocation_method            = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = azurerm_subnet.vmss.id
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = azurerm_public_ip.jumpbox.id
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = var.location
  resource_group_name   = azurerm_resource_group.vmss.name
  network_interface_ids = [azurerm_network_interface.jumpbox.id]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "jumpbox-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  os_profile {
    computer_name  = "jumpbox"
    admin_username = "azureuser"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = file("~/.ssh/id_rsa.pub")
    }
  }

  tags {
    environment = "codelab"
  }
}
```

A `outputs.tf` telepítés befejezésekor a következő kód hozzáadásához adja hozzá a következő kódot:

```
output "jumpbox_public_ip" {
    value = azurerm_public_ip.jumpbox.fqdn
}
```

## <a name="deploy-the-jumpbox"></a>A jumpbox üzembe helyezése

Helyezze üzembe a jumpboxot.

```bash
terraform apply 
```

A központi telepítés befejezése után az erőforráscsoport tartalma a következő képhez hasonlóan néz ki:

![A Terraform virtuálisgép-méretezési csoportjának erőforráscsoportja](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> A jelszóval történő bejelentkezés le van tiltva a jumpboxon és a virtuálisgép-méretezési készleten, amelyet telepített. Jelentkezzen be az SSH-val a virtuális gépek eléréséhez.

## <a name="clean-up-the-environment"></a>A környezet fölösleges elemeinek az eltávolítása

A következő parancsok törlik a jelen oktatóanyagban létrehozott erőforrásokat:

```bash
terraform destroy
```

Írja be *az igen* értéket, amikor a rendszer az erőforrások törlésének megerősítését kéri. Az eltávolítási folyamat eltarthat pár percig.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [További információ a Terraform azure-beli használatáról](/azure/terraform)
