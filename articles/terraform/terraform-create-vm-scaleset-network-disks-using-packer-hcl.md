---
title: Azure-beli virtuálisgép-méretezési csoport létrehozása a Terraformmal egy egyéni Packer-rendszerképből
description: A Terraform használatával a Packer által létrehozott egyéni rendszerképből konfigurálhatja és verziószámozhatja az Azure-beli virtuális gépek méretezési csoportját (virtuális hálózattal és felügyelt csatolt lemezekkel együtt).
services: terraform
ms.service: terraform
keywords: terraform, devops, méretezési csoport, virtuális gép, hálózat, tároló, modulok, egyéni rendszerképek, packer
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/29/2017
ms.openlocfilehash: 12c6ebc9b29c356ad169dbd921e71c7aa0c273b7
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54077504"
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image"></a>Azure-beli virtuálisgép-méretezési csoport létrehozása a Terraformmal egy egyéni Packer-rendszerképből

Ebben az oktatóanyagban a [Terraform](https://www.terraform.io/) használatával hoz létre és helyez üzembe a [Packer](https://www.packer.io/intro/index.html) által készített egyéni rendszerképpel létrehozott [Azure-beli virtuálisgép-méretezési csoportot](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) felügyelt lemezekkel a [HashiCorp Configuration Language](https://www.terraform.io/docs/configuration/syntax.html) (HCL) használatával.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Terraform-környezet beállítása
> * Változók és kimenetek használata egy Terraform-környezethez 
> * Hálózati infrastruktúra létrehozása és üzembe helyezése
> * Egyéni virtuálisgép-rendszerkép létrehozása a Packer használatával
> * Virtuálisgép-méretezési csoport létrehozása és üzembe helyezése az egyéni rendszerképpel
> * Jumpbox létrehozása és üzembe helyezése 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="before-you-begin"></a>Előkészületek
> * [A Terraform telepítése és az Azure-hoz való hozzáférés konfigurálása](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure)
> * [Egy SSH-kulcspár létrehozása](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys), ha még nem rendelkezik kulcspárral
> * [A Packer telepítése](https://www.packer.io/docs/install/index.html), ha még nincs telepítve a helyi gépen


## <a name="create-the-file-structure"></a>A fájlstruktúra létrehozása

Hozzon létre három új fájlt egy üres könyvtárban a következő nevekkel:

- ```variables.tf``` Ez a fájl tartalmazza a sablonban használt változók értékeit.
- ```output.tf``` Ez a fájl írja le az üzembe helyezés után megjelenő beállításokat.
- ```vmss.tf``` Ez a fájl tartalmazza az üzembe helyezés alatt álló infrastruktúra kódját.

##  <a name="create-the-variables"></a>A változók létrehozása 

Ebben a lépésben a Terraform által létrehozott erőforrások beállítására szolgáló változókat adhatja meg.

Szerkessze a `variables.tf` fájlt, másolja ki a következő kódot, majd mentse a módosításokat.

```tf 
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
> A resource_group_name változó alapértelmezett értéke nincs beállítva, ezért adjon meg egy saját értéket.

Mentse a fájlt.

A Terraform-sablon üzembe helyezésekor az alkalmazás eléréséhez használt teljes tartománynevet szeretné lekérni. A Terraform ```output``` erőforrástípusát használja, és kérje le az erőforrás ```fqdn``` tulajdonságát. 

Szerkessze az `output.tf` fájlt, és másolja ki az alábbi kódot, hogy megkapja a virtuális gépek teljes tartománynevét. 

```hcl 
output "vmss_public_ip" {
    value = "${azurerm_public_ip.vmss.fqdn}"
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>A hálózati infrastruktúra meghatározása egy sablonban 

Ebben a lépésben az alábbi hálózati infrastruktúrát hozza létre egy új Azure-erőforráscsoportban: 
  - Egy VNET a következő címtérrel: 10.0.0.0/16 
  - Egy alhálózat a következő címtérrel: 10.0.2.0/24
  - Két nyilvános IP-cím. Az egyiket a virtuálisgép-méretezési csoport terheléselosztója használja, a másik az SSH jumpboxhoz való csatlakozáshoz szükséges.

Egy erőforráscsoportra is szüksége van, ahol az összes erőforrás létrejön. 

Szerkessze és másolja be az alábbi kódot a ```vmss.tf``` fájlba: 

```tf 

resource "azurerm_resource_group" "vmss" {
  name     = "${var.resource_group_name}"
  location = "${var.location}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = "${azurerm_resource_group.vmss.name}"
  virtual_network_name = "${azurerm_virtual_network.vmss.name}"
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> Az Azure-on üzembe helyezett erőforrások címkézését javasoljuk, hogy a jövőben könnyebben azonosíthatók legyenek.

## <a name="create-the-network-infrastructure"></a>A hálózati infrastruktúra létrehozása

A Terraform-környezet inicializálásához futtassa a következő parancsot a `.tf` fájlok létrehozásához használt könyvtárban:

```bash
terraform init 
```
 
A szolgáltató beépülő moduljait a rendszer a Terraform-beállításjegyzékből abban a könyvtárban lévő ```.terraform``` mappába tölti le, ahol futtatta a parancsot.

Futtassa az alábbi parancsot az infrastruktúra az Azure-ban történő üzembe helyezéséhez.

```bash
terraform apply
```

Győződjön meg arról, hogy a nyilvános IP-cím teljes tartományneve megfeleljen a konfigurációnak.

![Virtuálisgép-méretezési csoport nyilvános IP-címének teljes Terraform-tartományneve](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

Az erőforráscsoport a következő erőforrásokat tartalmazza:

![Virtuálisgép-méretezési csoport hálózati Terraform-erőforrásai](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-using-packer"></a>Azure-rendszerkép létrehozása a Packer használatával
Az [Azure-ban a Linux rendszerű virtuális gépek rendszerképeinek Packerrel való létrehozását](https://docs.microsoft.com/azure/virtual-machines/linux/build-image-with-packer) tárgyaló oktatóanyagban leírt lépésekkel egyéni Linux-rendszerképet hozhat létre.
 
Kövesse az oktatóanyagot egy NGINX-telepítést tartalmazó, üzemből kivont Ubuntu-rendszerkép létrehozásához.

![A Packer-rendszerkép létrehozása után egy rendszerképpel rendelkezik](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Ebben az oktatóanyagban a Packer-rendszerképben egy parancs fut az nginx telepítéséhez. A létrehozás alatt saját szkriptet is futtathat.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Az infrastruktúra szerkesztése a virtuálisgép-méretezési csoport hozzáadásához

Ebben a lépésben a következő erőforrásokat hozza létre a korábban üzembe helyezett hálózaton:
- Az Azure Load Balancer az alkalmazás kiszolgálásához és a 4. lépésben üzembe helyezett nyilvános IP-címhez való csatolásához
- Egy Azure Load Balancer és az alkalmazást kiszolgáló szabályok, és ezek csatolása a korábban konfigurált nyilvános IP-címhez.
- Egy Azure-háttércímkészlet és annak hozzárendelése a terheléselosztóhoz 
- A terheléselosztón konfigurált és az alkalmazás által használt állapotminta portja 
- A korábban üzembe helyezett virtuális hálózaton futó terheléselosztó mögötti virtuálisgép-méretezési csoport
- [Nginx](http://nginx.org/) az egyéni rendszerképből telepített virtuálisgép-méretezési csoport csomópontjain


Adja hozzá az alábbi kódot a `vmss.tf` fájl végéhez.

```tf


resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = "${azurerm_public_ip.vmss.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "ssh-running-probe"
  port                = "${var.application_port}"
}

resource "azurerm_lb_rule" "lbnatrule" {
  resource_group_name            = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id                = "${azurerm_lb.vmss.id}"
  name                           = "http"
  protocol                       = "Tcp"
  frontend_port                  = "${var.application_port}"
  backend_port                   = "${var.application_port}"
  backend_address_pool_id        = "${azurerm_lb_backend_address_pool.bpepool.id}"
  frontend_ip_configuration_name = "PublicIPAddress"
  probe_id                       = "${azurerm_lb_probe.vmss.id}"
}

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = "${data.azurerm_resource_group.image.name}"
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    id="${data.azurerm_image.image.id}"
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
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = "${azurerm_subnet.vmss.id}"
      load_balancer_backend_address_pool_ids = ["${azurerm_lb_backend_address_pool.bpepool.id}"]
    }
  }
  
  tags {
    environment = "codelab"
  }
}

```

A következő kód a `variables.tf` fájlhoz adásával testreszabhatja az üzemelő példányt:

```tf 
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
- Hálózati adapter, amely ugyanahhoz az alhálózathoz csatlakozik, mint a virtuálisgép-méretezési csoport
- Ezzel a hálózati adapterrel rendelkező virtuális gép

Adja hozzá az alábbi kódot a `vmss.tf` fájl végéhez:

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = "${azurerm_subnet.vmss.id}"
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = "${azurerm_public_ip.jumpbox.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = "${var.location}"
  resource_group_name   = "${azurerm_resource_group.vmss.name}"
  network_interface_ids = ["${azurerm_network_interface.jumpbox.id}"]
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
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  tags {
    environment = "codelab"
  }
}
```

Szerkessze az `outputs.tf` fájlt a következő kód hozzáadásával, amely megjeleníti a jumpbox gazdagépnevét az üzembe helyezés befejezésekor:

```
output "jumpbox_public_ip" {
    value = "${azurerm_public_ip.jumpbox.fqdn}"
}
```

## <a name="deploy-the-jumpbox"></a>A jumpbox üzembe helyezése

Helyezze üzembe a jumpboxot.

```bash
terraform apply 
```

Az üzembe helyezés befejeztével az erőforráscsoport tartalma a következő képhez hasonlóan néz ki:

![A Terraform virtuálisgép-méretezési csoportjának erőforráscsoportja](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> A jelszóval történő bejelentkezés le van tiltva a jumpboxon és az üzembe helyezett virtuálisgép-méretezési csoporton. A virtuális gépek eléréséhez jelentkezzen be SSH-val.

## <a name="clean-up-the-environment"></a>A környezet fölösleges elemeinek az eltávolítása

A következő parancsok törlik a jelen oktatóanyagban létrehozott erőforrásokat:

```bash
terraform destroy
```

Írja be a `yes` választ, amikor a rendszer az erőforrások törlésének megerősítését kéri. Az eltávolítási folyamat eltarthat pár percig.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy virtuálisgép-méretezési csoportot és egy jumpboxot helyezett üzembe az Azure-ban a Terraform használatával. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Terraform-környezet inicializálása
> * Változók és kimenetek használata egy Terraform-környezethez 
> * Hálózati infrastruktúra létrehozása és üzembe helyezése
> * Egyéni virtuálisgép-rendszerkép létrehozása a Packer használatával
> * Virtuálisgép-méretezési csoport létrehozása és üzembe helyezése az egyéni rendszerképpel
> * Jumpbox létrehozása és üzembe helyezése 