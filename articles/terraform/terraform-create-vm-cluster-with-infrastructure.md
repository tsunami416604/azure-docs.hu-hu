---
title: Virtuálisgép-fürt létrehozása Terraformmal és HCL-lel
description: Linux rendszerű virtuális gép létrehozása egy terheléselosztóval az Azure-ban a Terraform és a HashiCorp konfigurációs nyelv (HCL) használatával
services: terraform
ms.service: azure
keywords: terraform, devops, virtuális gép, hálózat, modulok
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/13/2017
ms.openlocfilehash: bf16d963a83bc720cc39e47cc928c1926a92859d
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771463"
---
# <a name="create-a-vm-cluster-with-terraform-and-hcl"></a>Virtuálisgép-fürt létrehozása Terraformmal és HCL-lel

Ez az oktatóanyag egy kisméretű számítási fürt létrehozását mutatja be a [HashiCorp konfigurációs nyelv](https://www.terraform.io/docs/configuration/syntax.html) (HCL) használatával. A konfiguráció egy terheléselosztót és két Linux rendszerű virtuális gépet hoz létre egy [rendelkezésre állási csoportban](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy), valamint az összes szükséges hálózati erőforrást.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Azure-beli hitelesítés beállítása
> * Terraform konfigurációs fájl létrehozása
> * A Terraform inicializálása
> * Terraform végrehajtási terv létrehozása
> * A Terraform végrehajtási terv alkalmazása

## <a name="1-set-up-azure-authentication"></a>1. Azure-beli hitelesítés beállítása

> [!NOTE]
> Ha [Terraform környezeti változókat használ](/azure/virtual-machines/linux/terraform-install-configure), vagy az [Azure Cloud Shellben](terraform-cloud-shell.md) futtatja ezt az oktatóanyagot, ugorja át ezt a szakaszt.

Ebben a szakaszban egy Azure-szolgáltatásnevet hozunk létre, valamint két Terraform konfigurációs fájlt, amelyek a rendszerbiztonsági tag hitelesítő adatait tárolják.

1. [Állítson be egy Azure AD-szolgáltatásnevet](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure), amelynek használatával a Terraform erőforrásokat foglalhat le az Azure-ban. A szolgáltatásnév létrehozása során jegyezze fel a subscription ID (előfizetés-azonosító), a tenant (bérlő), az appId (alkalmazásazonosító) és a password (jelszó) értékét.

2. Nyisson meg egy parancssort.

3. Hozzon létre egy üres könyvtárat a Terraform-fájlok tárolására.

4. Hozzon létre egy új fájlt, amelyben a változókat deklarálja majd. A fájlnak bármilyen nevet adhat a `.tf` kiterjesztéssel.

5. Másolja az alábbi kódot a változódeklarációs fájlba:

  ```tf
  variable subscription_id {}
  variable tenant_id {}
  variable client_id {}
  variable client_secret {}
  
  provider "azurerm" {
      subscription_id = "${var.subscription_id}"
      tenant_id = "${var.tenant_id}"
      client_id = "${var.client_id}"
      client_secret = "${var.client_secret}"
  }
  ```

6. Hozzon létre egy új fájlt a Terraform-változók értékeinek tárolására. A Terraform-változók fájlját szokás a `terraform.tfvars` néven elnevezni, mivel a Terraform automatikusan betölti a `terraform.tfvars` (vagy a `*.auto.tfvars` mintát követő) nevű fájlt, ha az aktuális könyvtárban található ilyen fájl. 

7. Másolja az alábbi kódot a változók fájljába. Ügyeljen arra, hogy cserélje le a helyőrzőket a következőképpen: A `subscription_id`, használja az Azure-előfizetés azonosítója futtatásakor megadott `az account set`. A `tenant_id` helyett használja az `az ad sp create-for-rbac` által visszaadott `tenant` értéket. A `client_id` helyett használja az `az ad sp create-for-rbac` által visszaadott `appId` értéket. A `client_secret` helyett használja az `az ad sp create-for-rbac` által visszaadott `password` értéket.

  ```tf
  subscription_id = "<azure-subscription-id>"
  tenant_id = "<tenant-returned-from-creating-a-service-principal>"
  client_id = "<appId-returned-from-creating-a-service-principal>"
  client_secret = "<password-returned-from-creating-a-service-principal>"
  ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Terraform konfigurációs fájl létrehozása

Ebben a szakaszban egy fájlt hozunk létre az infrastruktúra erőforrás-definícióinak tárolására.

1. Hozzon létre egy új fájlt `main.tf` néven. 

2. Másolja az alábbi erőforrás-definíció mintákat az újonnan létrehozott `main.tf` fájlba: 

  ```tf
  resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
  }

  resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"
  }

  resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    virtual_network_name = "${azurerm_virtual_network.test.name}"
    address_prefix       = "10.0.2.0/24"
  }

  resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    public_ip_address_allocation = "static"
  }

  resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = "${azurerm_public_ip.test.id}"
    }
  }

  resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = "${azurerm_resource_group.test.name}"
    loadbalancer_id     = "${azurerm_lb.test.id}"
    name                = "BackEndAddressPool"
  }

  resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = "${azurerm_subnet.test.id}"
      private_ip_address_allocation = "dynamic"
      load_balancer_backend_address_pools_ids = ["${azurerm_lb_backend_address_pool.test.id}"]
    }
  }

  resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = "${azurerm_resource_group.test.location}"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
  }

  resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
  }

  resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = "${azurerm_resource_group.test.location}"
    availability_set_id   = "${azurerm_availability_set.avset.id}"
    resource_group_name   = "${azurerm_resource_group.test.name}"
    network_interface_ids = ["${element(azurerm_network_interface.test.*.id, count.index)}"]
    vm_size               = "Standard_DS1_v2"

    # Uncomment this line to delete the OS disk automatically when deleting the VM
    # delete_os_disk_on_termination = true

    # Uncomment this line to delete the data disks automatically when deleting the VM
    # delete_data_disks_on_termination = true

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "myosdisk${count.index}"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    # Optional data disks
    storage_data_disk {
      name              = "datadisk_new_${count.index}"
      managed_disk_type = "Standard_LRS"
      create_option     = "Empty"
      lun               = 0
      disk_size_gb      = "1023"
    }

    storage_data_disk {
      name            = "${element(azurerm_managed_disk.test.*.name, count.index)}"
      managed_disk_id = "${element(azurerm_managed_disk.test.*.id, count.index)}"
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = "${element(azurerm_managed_disk.test.*.disk_size_gb, count.index)}"
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags {
      environment = "staging"
    }
  }
  ```

## <a name="3-initialize-terraform"></a>3. A Terraform inicializálása 

A [terraform init parancs](https://www.terraform.io/docs/commands/init.html) egy könyvtárat inicializál, amely a Terraform konfigurációs fájljait tartalmazza – azaz az előző szakaszokban létrehozott fájlokat. Az új Terraform-konfigurációk összeállítása után mindig érdemes futtatni a `terraform init` parancsot. 

> [!TIP]
> A `terraform init` parancs idempotens, tehát többször is meghívható, és mindig ugyanazt az eredményt adja. Tehát ha együttműködési környezetben dolgozik, és úgy gondolja, hogy a konfigurációs fájlok esetleg módosultak, célszerű minden esetben meghívni a `terraform init` parancsot a tervek végrehajtása vagy alkalmazása előtt.

A Terraform inicializálásához futtassa az alábbi parancsot:

  ```cmd
  terraform init
  ```

  ![Terraform inicializálása](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Terraform végrehajtási terv létrehozása

A [terraform plan parancs](https://www.terraform.io/docs/commands/plan.html) egy végrehajtási tervet hoz létre. A végrehajtási terv összeállításához a Terraform összesíti az összes `.tf` fájlt az aktuális könyvtárban. 

Ha együttműködési környezetben dolgozik, ahol a konfiguráció a végrehajtási terv létrehozásának és alkalmazásának időpontja között módosulhat, a [terraform plan parancs -out paraméterével](https://www.terraform.io/docs/commands/plan.html#out-path) egy fájlba mentheti a végrehajtási tervet. Amennyiben azonban csak egyszemélyes környezetben dolgozik, ki is hagyhatja az `-out` paramétert.

Ha a Terraform-változók fájljának neve nem `terraform.tfvars`, és nem követi az `*.auto.tfvars` mintát sem, a fájlnevet is meg kell adnia a [-var-file paraméter](https://www.terraform.io/docs/commands/plan.html#var-file-foo) használatával a `terraform plan` parancs futtatásakor.

A `terraform plan` parancs feldolgozásakor a Terraform végrehajt egy frissítést, és meghatározza a konfigurációs fájlokban megadott célállapot eléréséhez szükséges műveleteket.

Ha a végrehajtási tervet nem szükséges mentenie, futtassa a következő parancsot:

  ```cmd
  terraform plan
  ```

Ha a végrehajtási tervet menteni kell, a következő parancsot futtassa (és cserélje le a &lt;path> helyőrzőt a kívánt mentési hely elérési útjára):

  ```cmd
  terraform plan -out=<path>
  ```

![Terraform végrehajtási terv létrehozása](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. A Terraform végrehajtási terv alkalmazása

A jelen oktatóanyag utolsó lépéseként a [terraform apply parancs](https://www.terraform.io/docs/commands/apply.html) használatával alkalmazzuk a `terraform plan` paranccsal összeállított műveleteket.

Ha a legfrissebb végrehajtási tervet szeretné alkalmazni, futtassa a következő parancsot:

  ```cmd
  terraform apply
  ```

Ha egy korábban mentett végrehajtási tervet szeretne alkalmazni, a következő parancsot futtassa (és cserélje le a &lt;path> helyőrzőt a mentett végrehajtási terv elérési útjára):

  ```cmd
  terraform apply <path>
  ```

![Terraform végrehajtási terv alkalmazása](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>További lépések

- Az [Azure Terraform-modulok](https://registry.terraform.io/modules/Azure) listájának áttekintése
- Hozzon létre egy [virtuálisgép-méretezési csoportot a Terraformmal](terraform-create-vm-scaleset-network-disks-hcl.md)
