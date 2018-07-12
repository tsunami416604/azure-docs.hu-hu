---
title: Egy Azure-beli virtuálisgép-méretezési csoport létrehozásához használja a Terraform beállítása
description: A Terraform konfigurálása és a egy Azure-beli virtuálisgép-méretezési csoport beállítása befejeződött egy virtuális hálózathoz, és felügyelt verzió használatával kapcsolatos oktatóanyagot csatlakoztatott lemezeket
keywords: a terraform, fejlesztés és üzemeltetés, a virtuális gépek, Azure, méretezhető beállítása, hálózati, tárolási, modulok
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 06/04/2018
ms.topic: article
ms.openlocfilehash: 5922bad24c50a9d315aae42ce11a33801b9dbcaf
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971833"
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set"></a>Egy Azure-beli virtuálisgép-méretezési csoport létrehozásához használja a Terraform beállítása

[Azure-beli virtuálisgép-méretezési csoportok](/azure/virtual-machine-scale-sets) lehetővé hozhat létre és kezelhet egy csoportot az azonos, elosztott terhelésű virtuális gépek, ahol a virtuálisgép-példányok száma automatikusan növelheti, vagy csökkenthető a pillanatnyi igényeknek megfelelően vagy egy meghatározott ütemezés betöltése. 

Ebben az oktatóanyagban megismerheti, hogyan használható [Azure Cloud Shell](/azure/cloud-shell/overview) a következő feladatok elvégzéséhez:

> [!div class="checklist"]
> * A Terraform üzembe helyezés beállítása
> * A Terraform központi telepítéshez használni változók és kimenetek 
> * Hozzon létre és hálózati infrastruktúra üzembe helyezése
> * Létrehozása és üzembe helyezése virtuálisgép-méretezési csoportot és csatlakoztat a hálózathoz
> * Létrehozni és üzembe helyezni a virtuális gépek SSH-n keresztül kapcsolódni a jumpbox

> [!NOTE]
> A Terraform ebben a cikkben használt konfigurációs fájlok a legújabb verziójában a [Soft Terraform tárházban a Githubon](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss).

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **A Terraform telepítése**: a cikk utasításait követve [Terraform hozzáférési szabályzatokat az Azure-bA](/azure/virtual-machines/linux/terraform-install-configure)

- **Hozzon létre SSH-kulcspárok**: Ha még nem rendelkezik egy SSH kulcspárok, kövesse a cikk a [létrehozása és a nyilvános és titkos ssh-kulcs használata Linux rendszerű virtuális gépekhez az Azure-ban](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/mac-create-ssh-keys).

## <a name="create-the-directory-structure"></a>A directory-struktúra létrehozása

1. Keresse meg a [az Azure portal](http://portal.azure.com).

1. Nyissa meg [az Azure Cloud Shell](/azure/cloud-shell/overview). Ha korábban nem jelölt ki egy környezetet, válassza ki a **Bash** a környezet.

    ![Cloud Shell kérése](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. Lépjen a `clouddrive` könyvtár.

    ```bash
    cd clouddrive
    ```

1. Hozzon létre egy könyvtárat nevű `vmss`.

    ```bash
    mkdir vmss
    ```

1. Módosítsa a könyvtárakat az új könyvtár:

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>A változók definíciók fájl létrehozása
Ebben a szakaszban adja meg a változókat, amelyek testre szabhatja a Terraform által létrehozott erőforrásokat.

Az Azure Cloud Shell belül hajtsa végre az alábbi lépéseket:

1. Hozzon létre egy fájlt `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Insert üzemmódba kiválasztásával a kulcsot.

1. Illessze be a következő kódot a szerkesztőbe:

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

1. Kilépés beszúrása módot az Esc billentyű kiválasztásával.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

    ```bash
    :wq
    ```

## <a name="create-the-output-definitions-file"></a>A definíciók kimeneti fájl létrehozása
Ebben a szakaszban létrehozza a fájlt, amely leírja a kimeneti üzembe helyezés után.

Az Azure Cloud Shell belül hajtsa végre az alábbi lépéseket:

1. Hozzon létre egy fájlt `output.tf`.

    ```bash
    vi output.tf
    ```

1. Insert üzemmódba kiválasztásával a kulcsot.

1. Illessze be a következő kódot a szerkesztő a teljesen minősített tartománynevét (FQDN) a virtuális gépek számára elérhetővé tenni. :

  ```JSON
    output "vmss_public_ip" {
        value = "${azurerm_public_ip.vmss.fqdn}"
    }
  ```

1. Kilépés beszúrása módot az Esc billentyű kiválasztásával.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

    ```bash
    :wq
    ```

## <a name="define-the-network-infrastructure-in-a-template"></a>A hálózati infrastruktúra meghatározása sablonban
Ebben a szakaszban található egy új Azure-erőforráscsoportot a következő hálózati infrastruktúra létrehozása: 

  - A 10.0.0.0/16 címterében egy virtuális hálózat (VNET) 
  - Egy alhálózat címtartománya a 10.0.2.0/24
  - Két nyilvános IP-címeket. Egy virtuális gép méretezési csoport terheléselosztó által, a másik az SSH jumpbox eléréséhez használt.

Az Azure Cloud Shell belül hajtsa végre az alábbi lépéseket:

1. Hozzon létre egy fájlt `vmss.tf` írja le a virtuálisgép-méretezési csoport infrastruktúra beállítása.

    ```bash
    vi vmss.tf
    ```

1. Insert üzemmódba kiválasztásával a kulcsot.

1. Illessze be a következő kódot a teljesen minősített tartománynevét (FQDN) a virtuális gépek számára elérhetővé a fájl végéhez. 

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

1. Kilépés beszúrása módot az Esc billentyű kiválasztásával.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

  ```bash
  :wq
  ```

## <a name="provision-the-network-infrastructure"></a>A hálózati infrastruktúra kiépítése
Használja az Azure Cloud Shell a címtárból, ahol létrehozta a konfigurációs fájlok (.tf) a következő lépésekkel:

1. A Terraform inicializálása.

  ```bash
  terraform init 
  ```

1. Futtassa a következő parancsot az Azure-ban a definiált infrastruktúra üzembe helyezését.

  ```bash
  terraform apply
  ```

  Terraform, "hely" értéket kéri a **hely** a változót `variables.tf`, de soha ne állítson be. Bármely érvényes helyet – például az "West US" Enter kiválasztása után adhat meg. (Bármilyen érték zárójelek használja a tárolóhelyek.)

1. A Terraform jelenít meg a kimenetet a `output.tf` fájlt. Ahogy az az alábbi képernyőfelvételen is látható, a teljes tartománynév formájában adható meg &lt;azonosítója >.&lt; Hely >. cloudapp.Azure.com formát követi. Az azonosító értéke a kiszámított érték, és a hely a Terraform futtatásakor adja meg az értéket.

  ![Virtuálisgép-méretezési csoport nyilvános IP-cím teljes tartománynév beállítása](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. Az Azure portál menüjében válassza **erőforráscsoportok** elemre.

1. Az a **erőforráscsoportok** lapon jelölje be **myResourceGroup** Terraform által létrehozott erőforrások megtekintéséhez.
  ![Virtuálisgép-méretezési csoport hálózati erőforrások](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Adjon hozzá egy virtuálisgép-méretezési csoportot

Ez a szakasz bemutatja a következő erőforrások hozzáadása a sablonhoz:

- Az Azure load balancer és szolgálja ki az alkalmazást, és mellékelje a nyilvános IP-címet, a cikkben korábban konfigurált szabályok
- Az Azure háttér-címkészletet, és rendelje hozzá a load balancer 
- Az alkalmazás által használt, és konfigurálta a terheléselosztó állapotát a mintavételi port 
- Állítsa be úgy a terheléselosztóhoz, amely a jelen cikkben korábban üzembe helyezett VNET futtat egy virtuálisgép-méretezési csoport
- [Az Nginx](http://nginx.org/) a virtuális gép méretezési használatával csomópontjaira [a cloud-init](http://cloudinit.readthedocs.io/en/latest/).

A Cloud Shellben hajtsa végre az alábbi lépéseket:

1. Nyissa meg a `vmss.tf` konfigurációs fájlt.

  ```bash
  vi vmss.tf
  ```

1. Nyissa meg a fájl végéhez, és adja meg a A kulcs kiválasztásával hozzáfűzéssel.

1. Illessze be a következő kódot a fájl végén:

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

1. Kilépés beszúrása módot az Esc billentyű kiválasztásával.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

    ```bash
    :wq
    ```

1. Hozzon létre egy fájlt `web.conf` , a cloud-init konfiguráció a méretezési csoportba tartozó virtuális gépek kiszolgálása érdekében. 

    ```bash
    vi web.conf
    ```

1. Insert üzemmódba kiválasztásával a kulcsot.

1. Illessze be a következő kódot a szerkesztőbe:

  ```JSON
  #cloud-config
  packages:
    - nginx
  ```

1. Kilépés beszúrása módot az Esc billentyű kiválasztásával.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

    ```bash
    :wq
    ```

1. Nyissa meg a `variables.tf` konfigurációs fájlt.

  ```bash
  vi variables.tf
  ```

1. Nyissa meg a fájl végéhez, és adja meg a A kulcs kiválasztásával hozzáfűzéssel.

1. A telepítés testreszabható illessze be a következő kódot a fájl végéhez:

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

1. Kilépés beszúrása módot az Esc billentyű kiválasztásával.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

    ```bash
    :wq
    ```

1. Hozzon létre egy Terraform tervet jelenítheti meg a virtuális gép méretezési csoport üzembe helyezését. (Kell adnia egy jelszót a kiválasztása, valamint az erőforrások helyét.)

  ```bash
  terraform plan
  ```

  A parancs kimenete az alábbi képernyőfelvételhez hasonló lesz:

  ![A virtuálisgép-méretezési készlet létrehozása kimenete](./media/terraform-create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Az Azure-ban az új erőforrások üzembe helyezése.

  ```bash
  terraform apply 
  ```

  A parancs kimenete az alábbi képernyőfelvételhez hasonló lesz:

  ![A Terraform virtuális gép méretezési erőforráscsoport](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. Nyisson meg egy böngészőt, és csatlakozzon a parancs által visszaadott teljes Tartománynevét. 

  ![Keresse meg a teljes tartománynév eredményei](./media/terraform-create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>Egy SSH-jumpbox hozzáadása
Ssh-t *jumpbox* egyetlen kiszolgáló, amely a "átlépünk" keresztül férhet hozzá a hálózaton lévő más kiszolgálókra. Ebben a lépésben konfigurálja a következőket:

- A hálózati adapter (vagy a jumpbox) csatlakozik a virtuálisgép-méretezési csoportot az azonos alhálózatban.

- A virtuális gép csatlakoztatva hálózati illesztőhöz. A jumpbox érhető el távolról. A csatlakozás után bármely, a méretezési csoportban lévő virtuális gépek ssh-n is.

1. Nyissa meg a `vmss.tf` konfigurációs fájlt.

  ```bash
  vi vmss.tf
  ```

1. Nyissa meg a fájl végéhez, és adja meg a A kulcs kiválasztásával hozzáfűzéssel.

1. Illessze be a következő kódot a fájl végén:

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

1. Nyissa meg a fájl végéhez, és adja meg a A kulcs kiválasztásával hozzáfűzéssel.

1. Illessze be a következő kódot a jumpbox állomásnevét megjelenítéséhez, ha az üzembe helyezés kész a fájl végén:

  ```
  output "jumpbox_public_ip" {
      value = "${azurerm_public_ip.jumpbox.fqdn}"
  }
  ```

1. Kilépés beszúrása módot az Esc billentyű kiválasztásával.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

    ```bash
    :wq
    ```

1. A jumpbox üzembe.

  ```bash
  terraform apply 
  ```

Az üzembe helyezés befejezése után a tartalom az erőforráscsoport hasonlít, amely az alábbi képernyőképen látható:

![A Terraform virtuális gép méretezési erőforráscsoport](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> Jelentkezzen be a jelszót lehetővé teszi a jumpboxot az le van tiltva, és a virtuális gép méretezési telepített. Jelentkezzen be ssh-KAPCSOLATOT a virtuális gép eléréséhez.

## <a name="environment-cleanup"></a>Környezet törlése 

A Terraform ebben az oktatóanyagban létrehozott erőforrások törléséhez adja meg a következő parancsot Cloud shellbe:

```bash
terraform destroy
```

A megsemmisítését folyamat több percet is igénybe vehet.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan hozzon létre egy Azure-beli virtuálisgép-méretezési csoportot a Terraform használatával. Az alábbiakban néhány további erőforrást többet szeretne megtudni az Azure-ban a Terraform segítségével: 

 [A Terraform Hub a Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Terraform az Azure-konfigurációszolgáltató dokumentációját](http://aka.ms/terraform)  
 [Terraform az Azure szolgáltatói forrás](http://aka.ms/tfgit)  
 [Terraform az Azure-modulok](http://aka.ms/tfmodules)