---
title: Oktatóanyag – Azure virtuálisgép-méretezési csoport létrehozása egy csomagoló egyéni rendszerképből a Terraform használatával
description: A Terraform használatával a Packer által létrehozott egyéni rendszerképből konfigurálhatja és verziószámozhatja az Azure-beli virtuális gépek méretezési csoportját (virtuális hálózattal és felügyelt csatolt lemezekkel együtt).
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 7d2813a51e63d86b56712bb6d07efc2f65ec65a0
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077816"
---
# <a name="tutorial-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image-by-using-terraform"></a>Oktatóanyag: Azure virtuálisgép-méretezési csoport létrehozása egy csomagoló egyéni rendszerképből a Terraform használatával

Ebben az oktatóanyagban a [Terraform](https://www.terraform.io/) használatával hozzon létre és helyezzen üzembe egy Azure-beli [virtuálisgép-méretezési készletet](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) , amelyet a [HashiCorp-konfigurációs nyelvet](https://www.terraform.io/docs/configuration/syntax.html) (HCl-t) használó, felügyelt lemezeket használó [csomagolóval](https://www.packer.io/intro/index.html) készített. 

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A Terraform üzembe helyezésének beállítása.
> * Változók és kimenetek használata a Terraform telepítéséhez.
> * Hozzon létre és helyezzen üzembe egy hálózati infrastruktúrát.
> * Hozzon létre egy egyéni virtuálisgép-rendszerképet a csomagoló használatával.
> * Hozzon létre és helyezzen üzembe egy virtuálisgép-méretezési csoportját az egyéni rendszerkép használatával.
> * Hozzon létre és helyezzen üzembe egy Jumpbox.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- **Terraform**: [telepítse a Terraform-t, és konfigurálja az Azure-hoz való hozzáférést](/azure/virtual-machines/linux/terraform-install-configure).
- **SSH-kulcspár**: [hozzon létre egy SSH-kulcspárt](/azure/virtual-machines/linux/mac-create-ssh-keys).
- **Csomagoló**: a [csomagoló telepítése](https://www.packer.io/docs/install/index.html).

## <a name="create-the-file-structure"></a>A fájlstruktúra létrehozása

Hozzon létre három új fájlt egy üres könyvtárban a következő nevekkel:

- `variables.tf`: Ez a fájl tartalmazza a sablonban használt változók értékeit.
- `output.tf`: Ez a fájl az üzembe helyezés után megjelenő beállításokat ismerteti.
- `vmss.tf`: Ez a fájl tartalmazza a telepítendő infrastruktúra kódját.

##  <a name="create-the-variables"></a>A változók létrehozása 

Ebben a lépésben a Terraform által létrehozott erőforrások beállítására szolgáló változókat adhatja meg.

Szerkessze a `variables.tf` fájlt, másolja a következő kódot, majd mentse a módosításokat.

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
> A resource_group_name változó alapértelmezett értéke nincs beállítva. Adja meg a saját értékét.

Mentse a fájlt.

A Terraform-sablon központi telepítésekor szeretné lekérni az alkalmazás eléréséhez használt teljes tartománynevet. A Terraform `output` erőforrástípusát használja, és kérje le az erőforrás `fqdn` tulajdonságát. 

Szerkessze az `output.tf` fájlt, és másolja ki az alábbi kódot, hogy megkapja a virtuális gépek teljes tartománynevét. 

```hcl 
output "vmss_public_ip" {
    value = azurerm_public_ip.vmss.fqdn
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>A hálózati infrastruktúra meghatározása egy sablonban 

Ebben a lépésben az alábbi hálózati infrastruktúrát hozza létre egy új Azure-erőforráscsoportban: 
  - Egy virtuális hálózat, amely a 10.0.0.0/16 címtartomány.
  - Egy alhálózat a 10.0.2.0/24 címtartomány.
  - Két nyilvános IP-cím. Az egyiket a virtuálisgép-méretezési csoport terheléselosztó használja. A másik az SSH-Jumpbox való kapcsolódásra szolgál.

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
> Az Azure-ban üzembe helyezett erőforrások címkézésének megjelölése a jövőben.

## <a name="create-the-network-infrastructure"></a>A hálózati infrastruktúra létrehozása

A Terraform-környezet inicializálásához futtassa a következő parancsot a `.tf` fájlok létrehozásához használt könyvtárban:

```bash
terraform init 
```
 
A szolgáltatói beépülő modulok a Terraform beállításjegyzékből tölthetők le a `.terraform` mappába abban a könyvtárban, ahol a parancsot futtatta.

Futtassa az alábbi parancsot az infrastruktúra az Azure-ban történő üzembe helyezéséhez.

```bash
terraform apply
```

Győződjön meg arról, hogy a nyilvános IP-cím teljes tartományneve megfeleljen a konfigurációnak.

![Virtuálisgép-méretezési csoport Terraform teljes tartományneve a nyilvános IP-címhez](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

Az erőforráscsoport a következő erőforrásokat tartalmazza:

![Virtuálisgép-méretezési csoport hálózati Terraform-erőforrásai](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-by-using-packer"></a>Azure-rendszerkép létrehozása a csomagoló használatával
Hozzon létre egy egyéni linuxos rendszerképet az oktatóanyag lépéseivel, amely [bemutatja, hogyan hozhat létre linuxos virtuálisgép-lemezképeket az Azure-ban a csomagoló használatával](/azure/virtual-machines/linux/build-image-with-packer).
 
Kövesse az oktatóanyagot, és hozzon létre egy felépített Ubuntu-rendszerképet az Nginx-mel.

![Miután létrehozta a csomagoló rendszerképet, rendelkezik egy képpel](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Ebben az oktatóanyagban a csomagoló rendszerképben egy parancs fut az Nginx telepítéséhez. A létrehozás alatt saját szkriptet is futtathat.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Az infrastruktúra szerkesztése a virtuálisgép-méretezési csoport hozzáadásához

Ebben a lépésben a következő erőforrásokat hozza létre a korábban üzembe helyezett hálózaton:
- Az alkalmazás kiszolgálására szolgáló Azure Load Balancer. Csatolja a korábban üzembe helyezett nyilvános IP-címhez.
- Egy Azure Load Balancer és az alkalmazás kiszolgálására szolgáló szabályok. Csatolja a korábban konfigurált nyilvános IP-címhez.
- Egy Azure háttérbeli címkészlet. Rendelje hozzá a terheléselosztó számára.
- Az alkalmazás által használt és a terheléselosztó által konfigurált állapot-mintavételi port.
- Egy virtuálisgép-méretezési csoport, amely a terheléselosztó mögött helyezkedik el, és a korábban üzembe helyezett virtuális hálózaton fut.
- [Nginx](https://nginx.org/) az egyéni rendszerképből telepített virtuálisgép-skála csomópontjain.


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
- A virtuálisgép-méretezési csoporttal azonos alhálózathoz csatlakozó hálózati adapter
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

`outputs.tf` szerkesztésével adja hozzá a következő kódot, amely megjeleníti a Jumpbox állomásnevét az üzembe helyezés befejezésekor:

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

Az üzembe helyezés befejezése után az erőforráscsoport tartalma a következő képhez hasonlóan néz ki:

![A Terraform virtuálisgép-méretezési csoportjának erőforráscsoportja](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> A jelszóval való bejelentkezés le van tiltva a Jumpbox és a telepített virtuálisgép-méretezési csoporton. Jelentkezzen be SSH-val a virtuális gépek eléréséhez.

## <a name="clean-up-the-environment"></a>A környezet fölösleges elemeinek az eltávolítása

A következő parancsok törlik a jelen oktatóanyagban létrehozott erőforrásokat:

```bash
terraform destroy
```

Adja meg az *Igen* értéket, ha a rendszer kéri, hogy erősítse meg az erőforrások törlését. Az eltávolítási folyamat eltarthat pár percig.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [További információ a Terraform Azure-beli használatáról](/azure/terraform)
