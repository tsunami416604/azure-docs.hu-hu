---
title: Oktatóanyag – Hozzon létre egy Azure Virtuálisgép-fürtet Terraform és HCL szolgáltatással
description: Ebben az oktatóanyagban a Terraform és a HCL segítségével hoz létre egy Linux virtuálisgép-fürtöt az Azure-ban.
keywords: azure devops terraform virtuálisgép-fürt
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: ae1b8eac15309ff27297d9472e70d32e68acaaac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945268"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-and-hcl"></a>Oktatóanyag: Hozzon létre egy Azure Virtuálisgép-fürtet Terraform és HCL-lel

Ebben az oktatóanyagban láthatja, hogyan hozhat létre egy kis számítási fürtet [a HCL](https://www.terraform.io/docs/configuration/syntax.html)használatával. 

Megtudhatja, hogyan kell elvégezni a következő feladatokat:

> [!div class="checklist"]
> * Állítsa be az Azure-hitelesítést.
> * Terraform konfigurációs fájl létrehozása.
> * Terheléselosztó létrehozásához használjon Terraform konfigurációs fájlt.
> * A Terraform konfigurációs fájl segítségével két Linux-virtuális gépet telepíthet egy rendelkezésre állási csoportban.
> * Inicializálja a Terraformot.
> * Terraform végrehajtási terv létrehozása.
> * Alkalmazza a Terraform végrehajtási tervet az Azure-erőforrások létrehozásához.

## <a name="1-set-up-azure-authentication"></a>1. Az Azure-hitelesítés beállítása

> [!NOTE]
> Ha [Terraform környezeti változókat használ](terraform-install-configure.md), vagy az [Azure Cloud Shellben](terraform-cloud-shell.md) futtatja ezt az oktatóanyagot, ugorja át ezt a szakaszt.

Ebben a szakaszban egy Azure-szolgáltatásnevet hozunk létre, valamint két Terraform konfigurációs fájlt, amelyek a rendszerbiztonsági tag hitelesítő adatait tárolják.

1. [Állítson be egy Azure AD-szolgáltatásnevet](terraform-install-configure.md#set-up-terraform-access-to-azure), amelynek használatával a Terraform erőforrásokat foglalhat le az Azure-ban. A szolgáltatásnév létrehozása során jegyezze fel a subscription ID (előfizetés-azonosító), a tenant (bérlő), az appId (alkalmazásazonosító) és a password (jelszó) értékét.

2. Nyisson meg egy parancssort.

3. Hozzon létre egy üres könyvtárat a Terraform-fájlok tárolására.

4. Hozzon létre egy új fájlt, amelyben a változókat deklarálja majd. A fájlnak bármilyen nevet adhat a `.tf` kiterjesztéssel.

5. Másolja az alábbi kódot a változódeklarációs fájlba:

   ```hcl
   variable subscription_id {}
   variable tenant_id {}
   variable client_id {}
   variable client_secret {}
  
   provider "azurerm" {
      version = "~>1.40"
     
      subscription_id = var.subscription_id
      tenant_id = var.tenant_id
      client_id = var.client_id
      client_secret = var.client_secret
   }
   ```

6. Hozzon létre egy új fájlt a Terraform-változók értékeinek tárolására. Gyakori, hogy a Terraform változófájlt `terraform.tfvars` Terraform ként nevezi `terraform.tfvars` el, automatikusan `*.auto.tfvars`betölt minden elnevezett fájlt (vagy követ egy mintát), ha az aktuális könyvtárban van. 

7. Másolja az alábbi kódot a változók fájljába. Ne felejtse lecserélni a helyőrzőket az alábbiak szerint: A `subscription_id` helyett használja az Azure-előfizetés az `az account set` parancs futtatásakor megadott azonosítóját. A `tenant_id` helyett használja az `az ad sp create-for-rbac` által visszaadott `tenant` értéket. A `client_id` helyett használja az `az ad sp create-for-rbac` által visszaadott `appId` értéket. A `client_secret` helyett használja az `az ad sp create-for-rbac` által visszaadott `password` értéket.

   ```hcl
   subscription_id = "<azure-subscription-id>"
   tenant_id = "<tenant-returned-from-creating-a-service-principal>"
   client_id = "<appId-returned-from-creating-a-service-principal>"
   client_secret = "<password-returned-from-creating-a-service-principal>"
   ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Terraform konfigurációs fájl létrehozása

Ebben a szakaszban egy fájlt hozunk létre az infrastruktúra erőforrás-definícióinak tárolására.

1. Hozzon létre egy új fájlt `main.tf` néven. 

2. Másolja az alábbi erőforrás-definíció mintákat az újonnan létrehozott `main.tf` fájlba: 

   ```hcl
   resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
   }

   resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name
   }

   resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = azurerm_resource_group.test.name
    virtual_network_name = azurerm_virtual_network.test.name
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    allocation_method            = "Static"
   }

   resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = azurerm_public_ip.test.id
    }
   }

   resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = azurerm_resource_group.test.name
    loadbalancer_id     = azurerm_lb.test.id
    name                = "BackEndAddressPool"
   }

   resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = azurerm_subnet.test.id
      private_ip_address_allocation = "dynamic"
    }
   }

   resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = azurerm_resource_group.test.location
    resource_group_name  = azurerm_resource_group.test.name
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
   }

   resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
   }

   resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = azurerm_resource_group.test.location
    availability_set_id   = azurerm_availability_set.avset.id
    resource_group_name   = azurerm_resource_group.test.name
    network_interface_ids = [element(azurerm_network_interface.test.*.id, count.index)]
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
      name            = element(azurerm_managed_disk.test.*.name, count.index)
      managed_disk_id = element(azurerm_managed_disk.test.*.id, count.index)
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = element(azurerm_managed_disk.test.*.disk_size_gb, count.index)
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = {
      environment = "staging"
    }
   }
   ```

## <a name="3-initialize-terraform"></a>3. Terraform inicializálása 

A [terraform init parancs](https://www.terraform.io/docs/commands/init.html) egy könyvtárat inicializál, amely a Terraform konfigurációs fájljait tartalmazza – azaz az előző szakaszokban létrehozott fájlokat. Célszerű mindig futtatni a parancsot `terraform init` egy új Terraform konfiguráció írása után. 

> [!TIP]
> A `terraform init` parancs idempotens, tehát többször is meghívható, és mindig ugyanazt az eredményt adja. Tehát ha együttműködési környezetben dolgozik, és úgy gondolja, hogy a konfigurációs fájlok esetleg módosultak, célszerű minden esetben meghívni a `terraform init` parancsot a tervek végrehajtása vagy alkalmazása előtt.

A Terraform inicializálásához futtassa az alábbi parancsot:

  ```bash
  terraform init
  ```

  ![Terraform inicializálása](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Terraform végrehajtási terv létrehozása

A [terraform plan parancs](https://www.terraform.io/docs/commands/plan.html) egy végrehajtási tervet hoz létre. A végrehajtási terv összeállításához a Terraform összesíti az összes `.tf` fájlt az aktuális könyvtárban. 

A [-out paraméter](https://www.terraform.io/docs/commands/plan.html#out-path) egy kimeneti fájlba menti a végrehajtási tervet. Ez a szolgáltatás a többfejlesztői környezetben gyakori egyidejűségi problémákat orvosolja. Az egyik ilyen probléma megoldódott a kimeneti fájl a következő forgatókönyv:

1. Dev 1 létrehozza a konfigurációs fájlt.
1. Dev 2 módosítja a konfigurációs fájlt.
1. A Dev 1 alkalmazza (futtatja) a konfigurációs fájlt.
1. Dev 1 váratlan eredményeket kap, nem tudva, hogy a Dev 2 módosította a konfigurációt.

A kimeneti fájl megadása 1 fejlesztői 1 fejlesztői 1 fejlesztői 1 fejlesztői 1- 

Ha nem kell mentenie a végrehajtási tervet, futtassa a következő parancsot:

  ```bash
  terraform plan
  ```

Ha mentenie kell a végrehajtási tervet, futtassa a következő parancsot. Cserélje le a helyőrzőket a környezetének megfelelő értékekre.

  ```bash
  terraform plan -out=<path>
  ```

Egy másik hasznos paraméter a [-var-file](https://www.terraform.io/docs/commands/plan.html#var-file-foo).

Alapértelmezés szerint a Terraform a következőképpen próbálta megtalálni a változófájlt:
- Nevű fájl`terraform.tfvars`
- A következő minta használatával elnevezett fájl:`*.auto.tfvars`

A változófájlnak azonban nem kell követnie az előző két konvenció egyikét sem. Ebben az esetben adja meg a `-var-file` változók fájlnevét azzal a paraméterrel, amelyben a változó fájlneve nem tartalmaz kiterjesztést. A következő példa ezt a pontot szemlélteti:

```hcl
terraform plan -var-file <my-variables-file>
```

A Terraform határozza meg a konfigurációs fájlban megadott állapot eléréséhez szükséges műveleteket.

![Terraform végrehajtási terv létrehozása](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. A Terraform végrehajtási terv alkalmazása

A jelen oktatóanyag utolsó lépéseként a [terraform apply parancs](https://www.terraform.io/docs/commands/apply.html) használatával alkalmazzuk a `terraform plan` paranccsal összeállított műveleteket.

Ha a legfrissebb végrehajtási tervet szeretné alkalmazni, futtassa a következő parancsot:

  ```bash
  terraform apply
  ```

Ha korábban mentett végrehajtási tervet szeretne alkalmazni, futtassa a következő parancsot. Cserélje le a helyőrzőket a környezetének megfelelő értékekre:

  ```bash
  terraform apply <path>
  ```

![Terraform végrehajtási terv alkalmazása](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Azure-beli virtuálisgép-méretezési csoport létrehozása a Terraform használatával](terraform-create-vm-scaleset-network-disks-hcl.md)