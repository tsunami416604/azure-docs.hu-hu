---
title: Egy Azure virtuálisgép-méretezési létrehozásához használjon Terraform beállítása
description: A csatlakoztatott lemezekkel Terraform konfigurálásához és egy Azure virtuálisgép-méretezési beállítása kész, de egy virtuális hálózatot, és felügyelt verzió használatára vonatkozó oktatóanyag
keywords: terraform, devops, virtuális gépek, az Azure, méretezhető beállítása, hálózati, tárolási, modulok
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 06/04/2018
ms.topic: article
ms.openlocfilehash: b7cd9ad90198ead7c68d838547232429dbd1289f
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757320"
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale"></a>Hozzon létre egy Azure virtuálisgép-méretezési Terraform segítségével

[Az Azure virtuálisgép-méretezési csoportok](/azure/virtual-machine-scale-sets) terheléselosztásos virtuális gépek, ahol a virtuálisgép-példányok száma automatikusan növelhető, és csökkenthető igény szerint vagy egy meghatározott ütemezés lehetővé teszi, létrehozását és kezelését egy csoportot az azonos, betölteni. 

Ebben az oktatóanyagban elsajátíthatja, hogyan használandó [Azure Cloud rendszerhéj](/azure/cloud-shell/overview) a következő feladatok végezhetők el:

> [!div class="checklist"]
> * Egy Terraform üzembe helyezés beállítása
> * Használja a változók és -kimenetek Terraform telepítéshez 
> * Hozzon létre, és a hálózati infrastruktúra telepítése
> * Létrehozása és központi telepítése egy virtuálisgép-méretezési csoport, és a hálózati csatlakoztatása
> * Létrehozhat és telepíthet egy jumpbox csatlakozni a virtuális gép SSH-kapcsolaton keresztül

> [!NOTE]
> A legújabb verziója található a konfigurációs fájlok, a cikk ezt használja Terraform a [Github tárházából Soft Terraform](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss).

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Terraform telepítése**: a cikk utasításait követve [Terraform és Azure való hozzáférés konfigurálása](/azure/virtual-machines/linux/terraform-install-configure)

- **Hozzon létre egy SSH-kulcspárral**: Ha még nem rendelkezik SSH kulcs pár, kövesse a cikkben lévő utasítások [létrehozása, és az SSH nyilvános és titkos kulcsból álló kulcspárt használata a Linux virtuális gépek Azure-ban](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/mac-create-ssh-keys).

## <a name="create-the-directory-structure"></a>A directory-struktúra létrehozása

1. Keresse meg a [Azure-portálon](http://portal.azure.com).

1. Nyissa meg [Azure felhőben rendszerhéj](/azure/cloud-shell/overview). Ha korábban nem jelölt ki egy olyan környezetben, válassza ki a **Bash** , a környezetben.

    ![Felhő rendszerhéj kérdés](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. Lépjen a `clouddrive` könyvtár.

    ```bash
    cd clouddrive
    ```

1. Hozzon létre egy könyvtárat nevű `vmss`.

    ```bash
    mkdir vmss
    ```

1. Váltson át az új könyvtár:

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>A változók definíciók fájl létrehozása
Ebben a szakaszban adja meg a változókat, amelyek testre szabhatja a Terraform által létrehozott erőforrásokat.

A Azure Cloud rendszerhéjból hajtsa végre az alábbi lépéseket:

1. Hozzon létre egy fájlt `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Adja meg a beszúrási módban kiválasztásával a kulcsot.

1. Az alábbi kód beillesztése a szerkesztőbe:

  ```JSON
  variable "location" {
    description = "The location where resources will be created"
  }

  variable "tags" {
    description = "A map of the tags to use for the resources that are deployed"
    type        = "map"

    default = {
      environment = "codelab"
    }
  }

  variable "resource_group_name" {
    description = "The name of the resource group in which the resources will be created"
    default     = "myResourceGroup"
  }
  ```

1. Kilépés az Esc billentyűt kiválasztásával beszúrási módban.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

    ```bash
    :wq
    ```

## <a name="create-the-output-definitions-file"></a>A definíciók a kimeneti fájl létrehozása
Ebben a szakaszban a fájlt, amely leírja a kimeneti alkalmazást hoz létre.

A Azure Cloud rendszerhéjból hajtsa végre az alábbi lépéseket:

1. Hozzon létre egy fájlt `output.tf`.

    ```bash
    vi output.tf
    ```

1. Adja meg a beszúrási módban kiválasztásával a kulcsot.

1. Az alábbi kód illessze be a szerkesztőt, és teszi közzé a teljesen minősített tartománynevét (FQDN) a virtuális gépek. :

  ```JSON
    output "vmss_public_ip" {
        value = "${azurerm_public_ip.vmss.fqdn}"
    }
  ```

1. Kilépés az Esc billentyűt kiválasztásával beszúrási módban.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

    ```bash
    :wq
    ```

## <a name="define-the-network-infrastructure-in-a-template"></a>A hálózati infrastruktúra meghatározhatja a sablonban
Ez a szakasz az alábbi hálózati infrastruktúrára, az új Azure erőforráscsoport létrehozása: 

  - Egy virtuális hálózathoz (VNET) rendelkező 10.0.0.0/16 címterében 
  - Egy alhálózat 10.0.2.0/24 címterében
  - Két nyilvános IP-címet. Egy virtuális gép méretezési készlet terheléselosztó által, a másik az SSH jumpbox való kapcsolódáshoz használt.

A Azure Cloud rendszerhéjból hajtsa végre az alábbi lépéseket:

1. Hozzon létre egy fájlt `vmss.tf` megadásával írhatja le a virtuálisgép-méretezési infrastruktúra beállítása.

    ```bash
    vi vmss.tf
    ```

1. Adja meg a beszúrási módban kiválasztásával a kulcsot.

1. Illessze be az alábbi kódot a teljesen minősített tartománynevét (FQDN) a virtuális gépek teszi közzé a fájl végét. 

  ```JSON
  resource "azurerm_resource_group" "vmss" {
    name     = "${var.resource_group_name}"
    location = "${var.location}"
    tags     = "${var.tags}"
  }

  resource "random_string" "fqdn" {
    length  = 6
    special = false
    upper   = false
    number  = false
  }

  resource "azurerm_virtual_network" "vmss" {
    name                = "vmss-vnet"
    address_space       = ["10.0.0.0/16"]
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"
    tags                = "${var.tags}"
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
    domain_name_label            = "${random_string.fqdn.result}"
    tags                         = "${var.tags}"
  }
  ```

1. Kilépés az Esc billentyűt kiválasztásával beszúrási módban.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

  ```bash
  :wq
  ```

## <a name="provision-the-network-infrastructure"></a>A hálózati infrastruktúra kiépítése
Az Azure-felhő rendszerhéj a könyvtárból, amelyben létrehozta a konfigurációs fájlok (.tf) végezze el az alábbi lépéseket:

1. Terraform inicializálni.

  ```bash
  terraform init 
  ```

1. A következő parancsot az Azure-ban meghatározott infrastruktúra központi telepítéséhez.

  ```bash
  terraform apply
  ```

  Terraform kérni fogja a "hely" értéket, mint a **hely** a változót `variables.tf`, de soha ne állítson be. Egyik érvényes helyen – például a "Nyugati US" Enter kiválasztása után adhat meg. (Zárójelek közé értéket használja, szóközök.)

1. Terraform kiírja a kimeneti meghatározottak szerint a `output.tf` fájlt. Az alábbi képernyőképen látható, a teljes tartománynév formájában történik &lt;azonosítója >.&lt; Hely >. cloudapp.azure.com. Az azonosító értéke, mert a számított érték és a hely Terraform futtatásakor adja meg az értéket.

  ![Virtuálisgép-méretezési készlet nyilvános IP-cím teljesen minősített tartományneve](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. Válassza ki az Azure-portál menüjében, **erőforráscsoportok** a fő menüből.

1. Az a **erőforráscsoportok** lapon jelölje be **myResourceGroup** Terraform által létrehozott erőforrások.
  ![Virtuálisgép-méretezési készlet hálózati erőforrások](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>A virtuálisgép-méretezési csoport hozzáadása

Ebben a szakaszban megismerheti, hogyan a következő erőforrások hozzáadása a sablonhoz:

- Egy Azure terheléselosztó és az alkalmazás szolgál, és csatlakoztassa azt a nyilvános IP-cím, a cikkben korábban konfigurált szabályok
- Egy Azure-háttérrendszernek-címkészlet, és rendelje hozzá a terheléselosztó 
- A health mintavételi portot, az alkalmazás által használt, és a terheléselosztó konfigurálva 
- Egy virtuálisgép-méretezési csoport végeznie, amely a cikkben korábban telepített virtuális hálózaton fut a terheléselosztó mögött
- [Nginx](http://nginx.org/) a virtuális gép méretezési használt csomópontjára [felhő inicializálás](http://cloudinit.readthedocs.io/en/latest/).

A felhő rendszerhéj hajtsa végre az alábbi lépéseket:

1. Nyissa meg a `vmss.tf` konfigurációs fájlt.

  ```bash
  vi vmss.tf
  ```

1. Nyissa meg a fájl végét, és adja meg a A kulcs kiválasztásával hozzáfűzéssel.

1. Illessze be a fájl végét a következő kódot:

  ```JSON
  resource "azurerm_lb" "vmss" {
    name                = "vmss-lb"
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"

    frontend_ip_configuration {
      name                 = "PublicIPAddress"
      public_ip_address_id = "${azurerm_public_ip.vmss.id}"
    }

    tags = "${var.tags}"
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
      name              = "osdisk"
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
      admin_username       = "${var.admin_user}"
      admin_password       = "${var.admin_password}"
      custom_data          = "${file("web.conf")}"
    }

    os_profile_linux_config {
      disable_password_authentication = false
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

    tags = "${var.tags}"
}
  ```

1. Kilépés az Esc billentyűt kiválasztásával beszúrási módban.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

    ```bash
    :wq
    ```

1. Hozzon létre egy fájlt `web.conf` szolgálhat a felhőalapú inicializálás konfigurációját a méretezési csoport részét képező virtuális gépeket. 

    ```bash
    vi web.conf
    ```

1. Adja meg a beszúrási módban kiválasztásával a kulcsot.

1. Az alábbi kód beillesztése a szerkesztőbe:

  ```JSON
  #cloud-config
  packages:
    - nginx
  ```

1. Kilépés az Esc billentyűt kiválasztásával beszúrási módban.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

    ```bash
    :wq
    ```

1. Nyissa meg a `variables.tf` konfigurációs fájlt.

  ```bash
  vi variables.tf
  ```

1. Nyissa meg a fájl végét, és adja meg a A kulcs kiválasztásával hozzáfűzéssel.

1. A központi telepítés testreszabása az alábbi kódot a fájl végét beillesztésével:

  ```JSON
  variable "application_port" {
      description = "The port that you want to expose to the external load balancer"
      default     = 80
  }

  variable "admin_user" {
      description = "User name to use as the admin account on the VMs that will be part of the VM Scale Set"
      default     = "azureuser"
  }

  variable "admin_password" {
      description = "Default password for admin account"
  }
  ``` 

1. Kilépés az Esc billentyűt kiválasztásával beszúrási módban.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

    ```bash
    :wq
    ```

1. A virtuálisgép-méretezési készlet telepítést megjelenítéséhez Terraform terv létrehozása. (Meg kell adnia egy jelszót a kiválasztásához, valamint az erőforrások helyét.)

  ```bash
  terraform plan
  ```

  A parancs az alábbi képernyőfelvételhez hasonló legyen:

  ![A virtuálisgép-méretezési csoport létrehozása kimenete](./media/terraform-create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Telepítheti az új erőforrásokat az Azure-ban.

  ```bash
  terraform apply 
  ```

  A parancs az alábbi képernyőfelvételhez hasonló legyen:

  ![Terraform virtuálisgép-méretezési csoport erőforráscsoport](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. Nyisson meg egy böngészőt, és kapcsolódjon a parancs által visszaadott teljes Tartománynevét. 

  ![Keresse meg az FQDN eredményei](./media/terraform-create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>Egy SSH jumpbox hozzáadása
Egy SSH *jumpbox* van egy különálló kiszolgálóét, de "ugorhat" révén a hálózati egyéb kiszolgálók eléréséhez. Ebben a lépésben konfigurálja a következőket:

- A hálózati illesztőt (vagy jumpbox) a virtuálisgép-méretezési csoport azonos alhálózaton csatlakoztatva.

- A virtuális gép csatlakoztatott hálózati illesztőhöz. A "jumpbox" érhető el távolról. A csatlakozás után a virtuális gépek, a méretezési csoportban lévő egyik SSH is.

1. Nyissa meg a `vmss.tf` konfigurációs fájlt.

  ```bash
  vi vmss.tf
  ```

1. Nyissa meg a fájl végét, és adja meg a A kulcs kiválasztásával hozzáfűzéssel.

1. Illessze be a fájl végét a következő kódot:

  ```JSON
  resource "azurerm_public_ip" "jumpbox" {
    name                         = "jumpbox-public-ip"
    location                     = "${var.location}"
    resource_group_name          = "${azurerm_resource_group.vmss.name}"
    public_ip_address_allocation = "static"
    domain_name_label            = "${random_string.fqdn.result}-ssh"
    tags                         = "${var.tags}"
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

    tags = "${var.tags}"
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
      admin_username = "${var.admin_user}"
      admin_password = "${var.admin_password}"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = "${var.tags}"
  }
  ```

1. Nyissa meg a `output.tf` konfigurációs fájlt.

  ```bash
  vi output.tf
  ```

1. Nyissa meg a fájl végét, és adja meg a A kulcs kiválasztásával hozzáfűzéssel.

1. Illessze be a jumpbox állomásnevét megjelenítendő, a telepítés befejezésekor a fájl végét a következő kódot:

  ```
  output "jumpbox_public_ip" {
      value = "${azurerm_public_ip.jumpbox.fqdn}"
  }
  ```

1. Kilépés az Esc billentyűt kiválasztásával beszúrási módban.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

    ```bash
    :wq
    ```

1. Telepítse a jumpbox.

  ```bash
  terraform apply 
  ```

A telepítés befejezése után a tartalom az erőforráscsoport hasonlít, amely az alábbi képernyőfelvételen látható módon:

![Terraform virtuálisgép-méretezési csoport erőforráscsoport](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> Jelentkezzen be a jelszót képes a jumpbox le van tiltva, és a virtuálisgép-méretezési csoport telepített. Jelentkezzen be a virtuális gépek eléréséhez SSH.

## <a name="environment-cleanup"></a>Környezet karbantartása 

Ebben az oktatóanyagban létrehozott Terraform erőforrások törléséhez felhő rendszerhéjat adja meg a következő parancsot:

```bash
terraform destroy
```

A megsemmisítése folyamat néhány percet vehet igénybe.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan Terraform segítségével hozzon létre egy Azure virtuálisgép-méretezési csoport. Az alábbiakban néhány további források további tudnivalók az Azure-on Terraform: 

 [A Microsoft.com Terraform Hub](https://docs.microsoft.com/azure/terraform/)  
 [Terraform Azure-szolgáltató dokumentáció](http://aka.ms/terraform)  
 [Terraform Azure szolgáltató forrás](http://aka.ms/tfgit)  
 [Terraform Azure modulok](http://aka.ms/tfmodules)