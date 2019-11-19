---
title: Oktatóanyag – Azure virtuálisgép-méretezési csoport létrehozása a Terraform használatával
description: Ismerje meg, hogyan konfigurálhatja és telepítheti az Azure virtuálisgép-méretezési csoportját a Terraform használatával.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: ef2ce0a3ea8c50123cd51ab60a6b98894739d859
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159074"
---
# <a name="tutorial-create-an-azure-virtual-machine-scale-set-using-terraform"></a>Oktatóanyag: Azure virtuálisgép-méretezési csoport létrehozása a Terraform használatával

Az [Azure virtuálisgép-méretezési](/azure/virtual-machine-scale-sets) csoportok lehetővé teszik azonos virtuális gépek konfigurálását. A virtuálisgép-példányok száma igény szerint vagy ütemezés alapján módosítható. További információ: [virtuálisgép-méretezési csoport automatikus skálázása a Azure Portalban](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-portal).

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a [Azure Cloud Shell](/azure/cloud-shell/overview) a következő feladatok végrehajtásához:

> [!div class="checklist"]
> * Terraform üzemelő példányának beállítása
> * Változók és kimenetek használata egy Terraform-környezethez
> * Hálózati infrastruktúra létrehozása és üzembe helyezése
> * Virtuálisgép-méretezési csoport létrehozása és csatolása a hálózathoz
> * Jumpbox létrehozása és üzembe helyezése, amely SSH-n keresztül csatlakozik a virtuális géphez

> [!NOTE]
> Az ebben a cikkben használt Terraform-konfigurációs fájlok legújabb verziója a [githubon a félelmetes Terraform-tárházban](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss)található.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **A Terraform telepítése**: Kövesse a [Terraform telepítését és az Azure-hozzáférés konfigurálását ismertető cikkben](/azure/virtual-machines/linux/terraform-install-configure) található utasításokat

- **SSH-kulcspár létrehozása**: további információért tekintse meg a [Linux rendszerű virtuális gépekhez készült nyilvános és titkos SSH-kulcspár létrehozása és használata az Azure-ban](/azure/virtual-machines/linux/mac-create-ssh-keys)című témakört.

## <a name="create-the-directory-structure"></a>A könyvtárstruktúra létrehozása

1. Keresse fel az [Azure Portalt](https://portal.azure.com).

1. Nyissa meg az [Azure Cloud Shellt](/azure/cloud-shell/overview). Ha még nem választott ki környezetet, válassza a **Bash** környezetet.

    ![Cloud Shell-parancssor](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. Lépjen be a `clouddrive` könyvtárba.

    ```bash
    cd clouddrive
    ```

1. Hozzon létre egy `vmss` nevű könyvtárat.

    ```bash
    mkdir vmss
    ```

1. Lépjen be az új könyvtárba:

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>A változódefiníciós fájl létrehozása
Ebben a szakaszban a Terraform által létrehozott erőforrások testre szabását végző változókat adhatja meg.

A Azure Cloud Shellon hajtsa végre a következő lépéseket:

1. Hozzon létre egy `variables.tf` nevű fájlt.

    ```bash
    code variables.tf
    ```

1. Másolja az alábbi kódot a szerkesztőbe:

   ```hcl
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

1. Mentse a fájlt ( **&lt;ctrl > S**), és lépjen ki a szerkesztőből ( **&lt;CTRL > Q**).

## <a name="create-the-output-definitions-file"></a>A kimenetdefiníciós fájl létrehozása
Ebben a szakaszban az üzembe helyezés utáni kimenetet leíró fájlt hozza létre.

A Azure Cloud Shellon hajtsa végre a következő lépéseket:

1. Hozzon létre egy `output.tf` nevű fájlt.

    ```bash
    code output.tf
    ```

1. Másolja az alábbi kódot a szerkesztőbe, hogy közzétegye a virtuális gépek teljes tartománynevét (FQDN).
   :

   ```hcl
    output "vmss_public_ip" {
        value = azurerm_public_ip.vmss.fqdn
    }
   ```

1. Mentse a fájlt ( **&lt;ctrl > S**), és lépjen ki a szerkesztőből ( **&lt;CTRL > Q**).

## <a name="define-the-network-infrastructure-in-a-template"></a>A hálózati infrastruktúra meghatározása egy sablonban
Ebben a szakaszban az alábbi hálózati infrastruktúrát hozza létre egy új Azure-erőforráscsoportban:

  - Egy virtuális hálózat (VNET) a következő címtérrel: 10.0.0.0/16
  - Egy alhálózat a következő címtérrel: 10.0.2.0/24
  - Két nyilvános IP-cím. Az egyiket a virtuálisgép-méretezési csoport terheléselosztója használja, a másik az SSH jumpboxhoz való csatlakozáshoz szükséges.

A Azure Cloud Shellon hajtsa végre a következő lépéseket:

1. Hozzon létre egy `vmss.tf` nevű fájlt a virtuálisgép-méretezési csoport infrastruktúrájának leírásához.

    ```bash
    code vmss.tf
    ```

1. Másolja az alábbi kódot a fájl végére, hogy közzétegye a virtuális gépek teljes tartománynevét (FQDN).

   ```hcl
   resource "azurerm_resource_group" "vmss" {
    name     = var.resource_group_name
    location = var.location
    tags     = var.tags
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
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name
    tags                = var.tags
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
    allocation_method = "Static"
    domain_name_label            = random_string.fqdn.result
    tags                         = var.tags
   }
   ```

1. Mentse a fájlt ( **&lt;ctrl > S**), és lépjen ki a szerkesztőből ( **&lt;CTRL > Q**).

## <a name="provision-the-network-infrastructure"></a>Hálózati infrastruktúra kiépítése
A konfigurációs fájlokat (. TF) létrehozó könyvtárból származó Azure Cloud Shell használatával hajtsa végre a következő lépéseket:

1. Inicializálja a Terraformot.

   ```bash
   terraform init
   ```

1. Futtassa az alábbi parancsot a megadott infrastruktúra üzembe helyezéséhez az Azure-ban.

   ```bash
   terraform apply
   ```

   A Terraform megkéri `location` érték megadását, mivel a `location` változó a `variables.tf`ben van definiálva, de soha nem van beállítva. Bármely érvényes helyet megadhat, például a „West US” értéket, majd nyomja le az Enter billentyűt. (Használjon zárójelet a szóközt tartalmazó értékekhez.)

1. A Terraform a `output.tf` fájlban megadottak szerint jeleníti meg a kimenetet. Ahogy az a következő képernyőképen is látható, a teljes tartománynév a következő formát ölti: `<ID>.<location>.cloudapp.azure.com`. Az azonosító a számított érték és a hely a Terraform futtatásakor megadott érték.

   ![A virtuálisgép-méretezési csoport teljes tartományneve a nyilvános IP-címhez](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. Az Azure Portal menüjében válassza az **Erőforráscsoportok** elemet a főmenüből.

1. Az **Erőforráscsoportok** lapon válassza **myResourceGroup** elemet a Terraform által létrehozott erőforrások megtekintéséhez.
   ![Virtuálisgép-méretezési csoport hálózati erőforrásai](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport hozzáadása

Ebben a szakaszban azt ismertetjük, hogyan adhatja hozzá az alábbi erőforrásokat a sablonhoz:

- Egy Azure Load Balancer és az alkalmazást kiszolgáló szabályok, és ezek csatolása az ebben a cikkben korábban konfigurált nyilvános IP-címhez
- Azure háttércímkészlet és hozzárendelése a terheléselosztóhoz
- A terheléselosztón konfigurált és az alkalmazás által használt állapotminta portja
- A cikkben korábban üzembe helyezett virtuális gépen futó terheléselosztó mögött lévő virtuálisgép-méretezési csoport
- [Nginx](https://nginx.org/) a virtuálisgép-méretezési csoport csomópontjain a [clud-init](https://cloudinit.readthedocs.io/en/latest/) használatával.

A Cloud Shellon hajtsa végre a következő lépéseket:

1. Nyissa meg a `vmss.tf` konfigurációs fájlt.

   ```bash
   code vmss.tf
   ```

1. Lépjen a fájl végéhez, és az A billentyű lenyomásával lépjen hozzáfűzési módba.

1. Illessze be az alábbi kódot a fájl végére:

   ```hcl
   resource "azurerm_lb" "vmss" {
    name                = "vmss-lb"
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name

    frontend_ip_configuration {
      name                 = "PublicIPAddress"
      public_ip_address_id = azurerm_public_ip.vmss.id
    }

    tags = var.tags
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
      admin_username       = var.admin_user
      admin_password       = var.admin_password
      custom_data          = file("web.conf")
    }

    os_profile_linux_config {
      disable_password_authentication = false
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

    tags = var.tags
   }
   ```

1. Mentse a fájlt, és lépjen ki a VI-szerkesztőből a következő parancs megadásával:

    ```bash
    :wq
    ```

1. Hozzon létre egy `web.conf` nevű fájlt, amely a méretezési csoportban szereplő virtuális gép clud-init konfigurációjaként szolgál.

    ```bash
    code web.conf
    ```

1. Másolja az alábbi kódot a szerkesztőbe:

   ```hcl
   #cloud-config
   packages:
    - nginx
   ```

1. Mentse a fájlt, és lépjen ki a VI-szerkesztőből a következő parancs megadásával:

     ```bash
     :wq
     ```

1. Nyissa meg a `variables.tf` konfigurációs fájlt.

    ```bash
    code variables.tf
    ```

1. Lépjen a fájl végéhez, és az A billentyű lenyomásával lépjen hozzáfűzési módba.

1. Testreszabhatja az üzemelő példányt, ha az alábbi kódot beilleszti a fájl végére:

    ```hcl
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

1. Mentse a fájlt ( **&lt;ctrl > S**), és lépjen ki a szerkesztőből ( **&lt;CTRL > Q**).

1. Hozzon létre egy Terraform-tervet a virtuálisgép-méretezési csoport üzembe helyezésének megjelenítéséhez. (Meg kell adnia egy tetszőleges jelszót, valamint az erőforrások helyét is.)

    ```bash
    terraform plan
    ```

    A parancs kimenetének az alábbi képernyőképhez hasonlóan kell kinéznie:

    ![Virtuálisgép-méretezési csoport létrehozásának kimenete](./media/terraform-create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Helyezze üzembe az új erőforrásokat az Azure-ban.

    ```bash
    terraform apply
    ```

    A parancs kimenetének az alábbi képernyőképhez hasonlóan kell kinéznie:

    ![A Terraform virtuálisgép-méretezési csoportjának erőforráscsoportja](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. Nyisson meg egy böngészőt, és kapcsolódjon a parancs által visszaadott FQDN-hez.

    ![Az FQDN keresésének eredménye](./media/terraform-create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>SSH-jumpbox hozzáadása
Az SSH- *Jumpbox* egyetlen kiszolgáló, amelyet a hálózaton lévő többi kiszolgáló elérésére használhat. Ebben a lépésben az alábbi erőforrásokat konfigurálja:

- Hálózati adapter (vagy jumpbox), amely ugyanahhoz az alhálózathoz csatlakozik, mint a virtuálisgép-méretezési csoport.

- Ezzel a hálózati adapterrel csatlakoztatott virtuális gép. Ez a jumpbox elérhető távolról is. A csatlakozás után SSH-n be tud jelentkezni a méretezési csoportban található bármely virtuális gépre.

1. Nyissa meg a `vmss.tf` konfigurációs fájlt.

   ```bash
   code vmss.tf
   ```

1. Lépjen a fájl végéhez, és az A billentyű lenyomásával lépjen hozzáfűzési módba.

1. Illessze be az alábbi kódot a fájl végére:

   ```hcl
   resource "azurerm_public_ip" "jumpbox" {
    name                         = "jumpbox-public-ip"
    location                     = var.location
    resource_group_name          = azurerm_resource_group.vmss.name
    allocation_method = "Static"
    domain_name_label            = "${random_string.fqdn.result}-ssh"
    tags                         = var.tags}
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

    tags = var.tags
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
      admin_username = var.admin_user
      admin_password = var.admin_password
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = var.tags
   }
   ```

1. Nyissa meg a `output.tf` konfigurációs fájlt.

   ```bash
   code output.tf
   ```

1. Lépjen a fájl végéhez, és az A billentyű lenyomásával lépjen hozzáfűzési módba.

1. Illessze be az alábbi kódot a fájl végére a jumpbox gazdanevének megjelenítéséhez, miután az üzembe helyezés befejeződött:

   ```hcl
   output "jumpbox_public_ip" {
      value = azurerm_public_ip.jumpbox.fqdn
   }
   ```

1. Mentse a fájlt ( **&lt;ctrl > S**), és lépjen ki a szerkesztőből ( **&lt;CTRL > Q**).

1. Helyezze üzembe a jumpboxot.

   ```bash
   terraform apply
   ```

Miután az üzembe helyezés befejeződött, az erőforráscsoport tartalma az alábbi képernyőképen láthatóhoz fog hasonlítani:

![A Terraform virtuálisgép-méretezési csoportjának erőforráscsoportja](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> A jelszóval történő bejelentkezés le van tiltva az üzembe helyezett jumpboxon és virtuálisgép-méretezési csoporton. A virtuális gép(ek) eléréséhez jelentkezzen be SSH-val.

## <a name="environment-cleanup"></a>Környezet tisztítása

Az ebben az oktatóanyagban létrehozott Terraform-erőforrások törléséhez írja be az alábbi parancsot a Cloud Shellbe:

```bash
terraform destroy
```

Az eltávolítási folyamat több percig is eltarthat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [További információ a Terraform Azure-beli használatáról](/azure/terraform)