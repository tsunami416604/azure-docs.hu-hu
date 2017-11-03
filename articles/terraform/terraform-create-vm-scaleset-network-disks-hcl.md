---
title: "Terraform használja, és hozzon létre egy Azure Virtuálisgép-méretezési Hardverkompatibilitási segítségével."
description: "Használjon Terraform konfigurálásához és egy Azure virtuálisgép-méretezési beállítása kész, de egy virtuális hálózatot, és felügyelt csatlakoztatott lemezekkel."
keywords: "terraform, devops, állítsa be, a virtuális gép, hálózati, tárolási, modulok méretezése"
ms.service: virtual-machines-linux
author: dcaro
ms.author: dcaro
ms.date: 10/04/2017
ms.topic: article
ms.openlocfilehash: 7a4e21d547b3d2b2399f9f68b1babd9f82a421b7
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="use-terraform-to-plan-and-create-a-networked-azure-vm-scale-set-with-managed-storage"></a>Terraform segítségével tervezze meg, és hozzon létre egy hálózati Azure Virtuálisgép-méretezési felügyelt tárolóval beállítása

Ebben a cikkben használata [Terraform](https://www.terraform.io/) hozhat létre és telepíthet egy [Azure virtuális gép scaleset](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) felügyelt lemezek használatával a [Hashicorp konfigurációs nyelvi](https://www.terraform.io/docs/configuration/syntax.html) (Hardverkompatibilitási).  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Terraform üzembe helyezés beállítása
> * Használja a változók és -kimenetek Terraform telepítéshez 
> * Hozzon létre, és a hálózati infrastruktúra telepítése
> * Létrehozása és központi telepítése egy virtuálisgép-méretezési csoport, és a hálózati csatlakoztatása
> * Létrehozhat és telepíthet egy jumpbox csatlakozni a virtuális gép SSH-kapcsolaton keresztül

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="before-you-begin"></a>Előkészületek

- [Terraform telepítése és konfigurálása az Azure-hozzáférést](/azure/virtual-machines/linux/terraform-install-configure)
- [Hozzon létre egy SSH-kulcspárral](/azure/virtual-machines/linux/mac-create-ssh-keys) Ha még nem rendelkezik.

## <a name="create-the-file-structure"></a>A fájl struktúra létrehozása

Hozzon létre három új fájlt a következő nevű üres könyvtár:

- `variables.tf`Ebben a fájlban található a sablon szerepel a változók értékeit.
- `output.tf`Ez a fájl, amely megjelenik a telepítés utáni-beállításokat ismerteti.
- `vmss.tf`Ez a virtuálisgép-méretezési kódkészlet infrastruktúra.

## <a name="create-the-variables-and-output-definitions"></a>A változók létrehozása és definíciókat

Ebben a lépésben adja meg, amely testre szabhatja az erőforrások Terraform által létrehozott változókat.

Szerkessze a `variables.tf` fájlt, másolja az alábbi kódot, majd mentse a változtatásokat.

```tf 
variable "location" {
  description = "The location where resources will be created"
  default     = "West US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources will be created"
  default     = "myResourceGroup"
}
```

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
  - Két nyilvános IP-címet. Egy virtuális gép méretezési készlet terheléselosztó által, a másik az SSH jumpbox való kapcsolódáshoz használt.


Szerkessze, és másolja az alábbi kód a `vmss.tf` fájlt: 

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
> Célszerű az erőforrásokat, lehetővé teszi a jövőben az azonosító az Azure-ban telepített címkét.

## <a name="create-the-network-infrastructure"></a>A hálózati infrastruktúra létrehozása

A Terraform környezet inicializálása a következő parancs futtatásával a címtárban hozta létre a `.tf` fájlok:

```bash
terraform init 
```

Futtassa a következő parancsot az Azure-infrastruktúra központi telepítéséhez.

```bash
terraform apply
```

Ellenőrizze, hogy megfelel-e a nyilvános IP-cím teljes Tartománynevét a konfiguráció: ![VMSS terraform FQDN nyilvános IP-cím](./media/tf-create-vmss-step4-fqdn.png)


Az erőforráscsoport kell rendelkeznie a következőket: ![VMSS terraform hálózati erőforrások](./media/tf-create-vmss-step4-rg.png)


## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Az infrastruktúra hozzáadása a virtuálisgép-méretezési csoport szerkesztése

Ebben a lépésben a következő források a sablon hozzáadása:

- Egy Azure terheléselosztó és az alkalmazás szolgál, és csatlakoztassa azt a nyilvános IP-címet korábban konfigurált szabályok betöltése.
- Azure-háttérrendszernek-címkészlet, és rendelje hozzá a terheléselosztó 
- A health mintavételi portot, az alkalmazás által használt, és a terheléselosztó konfigurálva 
- Egy virtuálisgép-méretezési csoport végeznie a korábban telepített virtuális hálózaton fut, a terheléselosztó mögött
- [Nginx](http://nginx.org/) egy egyéni parancsprogramok futtatására szolgáló bővítmény használatával virtuálisgép-méretezési csomópontjára.

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
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
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

  extension { 
    name = "vmssextension"
    publisher = "Microsoft.OSTCExtensions"
    type = "CustomScriptForLinux"
    type_handler_version = "1.2"
    settings = <<SETTINGS
    {
        "commandToExecute": "sudo apt-get -y install nginx"
    }
    SETTINGS
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

A parancs a következő hasonlóan kell kinéznie.
![Terraform vmss terv hozzáadása](./media/tf-create-vmss-step6-plan884d3aefd9708a711bc09a66e85eb149c23a3ccff959655ec00418168b2bd481.png)

Ezután telepítheti az további erőforrásokat az Azure-ban: 

```bash
terraform apply 
```

A tartalom az erőforráscsoport hasonlóan kell kinéznie:

![Terraform vm scaleset erőforráscsoport](./media/tf-create-vmss-step6-apply.png)

Nyisson meg egy böngészőt, és kapcsolódjon a parancs által visszaadott teljes Tartománynevét. 

## <a name="add-an-ssh-jumpbox-to-the-existing-network"></a>Egy SSH jumpbox hozzáadása a meglévő hálózati 

Ebben a lépésben konfigurálja a következőket:
- Egy adott hálózati csatoló kapcsolódik a virtuálisgép-méretezési csoport azonos alhálózaton.
- A virtuális gép csatlakoztatott hálózati illesztőhöz. A "jumpbox" érhető el távolról. A csatlakozás után a virtuális gépek, a méretezési csoportban lévő egyik SSH is.



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

Szerkesztés `outputs.tf` , és adja hozzá az alábbi kódot a jumpbox állomásnevét jeleníti meg, a telepítés befejezésekor:

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

A telepítés befejezése után a tartalom az erőforráscsoport néz ki:

![Terraform vm scaleset erőforráscsoport](./media/tf-create-create-vmss-step8.png)

> [!NOTE]
> Jelentkezzen be egy jelszót a jumpbox le van tiltva, és a virtuálisgép-méretezési csoport telepített. Jelentkezzen be a virtuális gépek eléréséhez SSH.

## <a name="clean-up-the-environment"></a>A környezet tisztítása

A következő parancsokat az oktatóanyagban szereplő létrejött erőforrásokat törlése:

```bash
terraform destroy
```

Típus `yes` során a rendszer rákérdez az erőforrás törlése. A megsemmisítése folyamat néhány percet is igénybe vehet.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy virtuálisgép-méretezési beállítása az Azure-ban Terraform telepítette. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Terraform telepítési inicializálása
> * Használja a változók és -kimenetek Terraform telepítéshez 
> * Hozzon létre, és a hálózati infrastruktúra telepítése
> * Hozzon létre és központi telepítése egy virtuálisgép-méretezési csoport, és csatlakoztassa azt egy meglévő környezetben
> * Létrehozhat és telepíthet egy jumpbox csatlakozni a virtuális gép SSH-kapcsolaton keresztül 
