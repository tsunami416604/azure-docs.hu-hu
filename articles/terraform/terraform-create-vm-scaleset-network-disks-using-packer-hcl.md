---
title: "Egy Azure virtuálisgép-méretezési létrehozásához használjon Terraform csomagoló egyéni lemezkép állítható be"
description: "Terraform konfigurálása és Azure virtuálisgép-méretezési csomagoló (kész, de a virtuális hálózat és a felügyelt csatlakoztatott lemezek) által létrehozott egyéni lemezképéről verzióját használja."
keywords: "terraform, devops, állítsa be, a virtuális gép, hálózati, tárolási, modulok, egyéni lemezképek, csomagoló méretezése"
author: VaijanathB
ms.author: tarcher
ms.date: 10/29/2017
ms.topic: article
ms.openlocfilehash: 284eae93de36986e41ba80f98f86495d8f34f57b
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image"></a>Egy Azure virtuálisgép-méretezési létrehozásához használjon Terraform csomagoló egyéni lemezkép állítható be

A jelen oktatóanyag esetében használja, [Terraform](https://www.terraform.io/) hozhat létre és telepíthet egy [Azure virtuálisgép-méretezési csoport](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) létrehozni egy egyéni lemezkép használatával létrehozott [csomagoló](https://www.packer.io/intro/index.html) felügyelt lemezekkel használja a [HashiCorp konfigurációs nyelvi](https://www.terraform.io/docs/configuration/syntax.html) (Hardverkompatibilitási).  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Terraform üzembe helyezés beállítása
> * Használja a változók és -kimenetek Terraform telepítéshez 
> * Hozzon létre, és a hálózati infrastruktúra telepítése
> * Hozzon létre egy egyéni virtuálisgép-lemezkép használata a csomagoló
> * Létrehozhat és telepíthet egy virtuálisgép-méretezési állítsa be az egyéni lemezkép használatával
> * Létrehozhat és telepíthet egy jumpbox 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="before-you-begin"></a>Előkészületek
> * [Terraform telepítése és konfigurálása az Azure-hozzáférést](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure)
> * [Hozzon létre egy SSH-kulcspárral](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) Ha még nem rendelkezik
> * [Telepítse a csomagoló](https://www.packer.io/docs/install/index.html) Ha még nincs telepítve a helyi számítógépre csomagoló


## <a name="create-the-file-structure"></a>A fájl struktúra létrehozása

Hozzon létre három új fájlt a következő nevű üres könyvtár:

- ```variables.tf```Ebben a fájlban található a sablon szerepel a változók értékeit.
- ```output.tf```Ez a fájl, amely megjeleníti a telepítés utáni-beállításokat ismerteti.
- ```vmss.tf```Ez a fájl tartalmazza az infrastruktúra központilag telepíteni a kódot.

##  <a name="create-the-variables"></a>A változók létrehozása 

Ebben a lépésben adja meg, amely testre szabhatja az erőforrások Terraform által létrehozott változókat.

Szerkessze a `variables.tf` fájlt, másolja az alábbi kódot, majd mentse a változtatásokat.

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
> A resource_group_name változó alapértelmezett értéke nem beállított, saját értéke határozza meg.

Mentse a fájlt.

A Terraform sablon telepítésekor le szeretné kérdezni az alkalmazás eléréséhez használt, teljesen minősített tartománynevét. Használja a ```output``` Terraform, és az erőforrás típusa a ```fqdn``` tulajdonság az erőforrás. 

Szerkessze a `output.tf` fájlt, és másolja az alábbi kódot teszi közzé a teljesen minősített tartománynevét az virtuális gépekhez. 

```hcl 
output "vmss_public_ip" {
    value = "${azurerm_public_ip.vmss.fqdn}"
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>A hálózati infrastruktúra meghatározhatja a sablonban 

Ebben a lépésben hozzon létre egy új Azure erőforráscsoport az alábbi hálózati infrastruktúrára: 
  - Egy VNET és 10.0.0.0/16 címterében 
  - Egy alhálózat 10.0.2.0/24 címterében
  - Két nyilvános IP-címet. Egy használják a virtuális gép méretezési készlet terheléselosztó; a másik az SSH jumpbox való kapcsolódáshoz használt

Kell egy olyan erőforráscsoport, ahol az erőforrások jönnek létre. 

Szerkessze, és másolja az alábbi kód a ```vmss.tf``` fájlt: 

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
> Azt javasoljuk, hogy az erőforrásokat, lehetővé teszi a jövőben az azonosító az Azure-ban telepített címkézést.

## <a name="create-the-network-infrastructure"></a>A hálózati infrastruktúra létrehozása

A Terraform környezet inicializálása a következő parancs futtatásával a címtárban hozta létre a `.tf` fájlok:

```bash
terraform init 
```
 
A szolgáltató beépülő modulok letöltése a Terraform beállításjegyzékből való a ```.terraform``` mappa a könyvtárban, ahol futtatta a parancsot.

A következő parancsot az Azure-infrastruktúra központi telepítéséhez.

```bash
terraform apply
```

Győződjön meg arról, hogy a teljes tartománynevét a nyilvános IP-cím a konfiguráció megfelel-e.

![Virtuálisgép-méretezési készlet Terraform teljes tartománynevét adja meg a nyilvános IP-cím](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

Az erőforráscsoport a következőket tartalmazza:

![Virtuálisgép-méretezési csoport Terraform hálózati erőforrások](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-using-packer"></a>Egy Azure csomagoló lemezkép létrehozása
Az oktatóanyag lépéseit egyéni Linux lemezkép létrehozása [csomagoló használata a Linux virtuális gép képek létrehozása az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/linux/build-image-with-packer).
 
Az oktatóanyag ismerteti, hozzon létre egy megszüntetett Ubuntu kép NGINX telepítve.

![A csomagoló lemezkép létrehozásához követően kép](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Ebben az oktatóanyagban a csomagoló kép alkalmazásában parancs futtatásával telepíti nginx. A saját parancsfájl létrehozása közben is futtathatja.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Az infrastruktúra hozzáadása a virtuálisgép-méretezési csoport szerkesztése

Ebben a lépésben a hálózaton, amelyek korábban központilag telepített hoz létre a következőket:
- Az Azure terheléselosztó a szolgál az alkalmazás és a nyilvános IP-cím, a 4. lépésben telepített csatlakoztatása
- Egy Azure terheléselosztó és az alkalmazás szolgál, és csatlakoztassa azt a nyilvános IP-címet korábban konfigurált szabályok betöltése.
- Azure-háttérrendszernek-címkészlet, és rendelje hozzá a terheléselosztó 
- A health mintavételi portot, az alkalmazás által használt, és a terheléselosztó konfigurálva 
- Egy virtuálisgép-méretezési csoport végeznie a korábban telepített virtuális hálózaton fut, a terheléselosztó mögött
- [Nginx](http://nginx.org/) egyéni lemezképből telepített virtuálisgép-méretezési csomópontjára


Adja hozzá a következő kódot végén a `vmss.tf` fájlt.

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

Adja hozzá az alábbi kódot a központi telepítés testreszabása `variables.tf`:

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


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>A virtuálisgép-méretezési beállítása az Azure telepítéséhez

A következő paranccsal jelenítheti meg a virtuálisgép-méretezési készlet telepítést:

```bash
terraform plan
```

A parancs az alábbi képen néz ki:

![Terraform hozzáadása a virtuálisgép-méretezési csoport terv](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-plan.png)

Telepítse a további erőforrások az Azure-ban: 

```bash
terraform apply 
```

Az erőforráscsoport a tartalom a következő kép néz ki:

![Terraform virtuálisgép-méretezési csoport erőforráscsoport](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

Nyisson meg egy böngészőt, és kapcsolódjon a parancs által visszaadott teljesen minősített tartománynevét. 


## <a name="add-a-jumpbox-to-the-existing-network"></a>Egy jumpbox hozzáadása a meglévő hálózati 

Ez az opcionális lépés lehetővé teszi, hogy az SSH-elérést a virtuálisgép-méretezési egy jumpbox segítségével példányára.

A következő erőforrások hozzáadása a meglévő telepítés:
- Egy adott hálózati csatoló kapcsolódik ugyanazon az alhálózaton, mint a virtuálisgép-méretezési csoport
- A virtuális gép hálózati illesztőhöz

Adja hozzá a következő kódot végén a `vmss.tf` fájlt:

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

Szerkesztés `outputs.tf` az alábbi kódot, amely a jumpbox állomásnevét jeleníti meg, a telepítés befejezésekor hozzáadása:

```
output "jumpbox_public_ip" {
    value = "${azurerm_public_ip.jumpbox.fqdn}"
}
```

## <a name="deploy-the-jumpbox"></a>A jumpbox telepítése

Telepítse a jumpbox.

```bash
terraform apply 
```

A telepítés befejezése után a tartalom az erőforráscsoport illusztráción láthatóhoz hasonló következő:

![Terraform virtuálisgép-méretezési csoport erőforráscsoport](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> Jelentkezzen be egy jelszót a jumpbox le van tiltva, és a virtuálisgép-méretezési csoport telepített. Jelentkezzen be a virtuális gépek eléréséhez SSH.

## <a name="clean-up-the-environment"></a>A környezet tisztítása

A következő parancsokat az oktatóanyagban szereplő létrejött erőforrásokat törlése:

```bash
terraform destroy
```

Típus `yes` során a rendszer rákérdez az erőforrás törlése. A megsemmisítése folyamat néhány percet vehet igénybe.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban telepítette, a virtuálisgép-méretezési csoport és egy Azure-ban Terraform jumpbox. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Terraform telepítési inicializálása
> * Használja a változók és -kimenetek Terraform telepítéshez 
> * Hozzon létre, és a hálózati infrastruktúra telepítése
> * Hozzon létre egy egyéni virtuálisgép-lemezkép használata a csomagoló
> * Létrehozhat és telepíthet egy virtuálisgép-méretezési állítsa be az egyéni lemezkép használatával
> * Létrehozhat és telepíthet egy jumpbox 