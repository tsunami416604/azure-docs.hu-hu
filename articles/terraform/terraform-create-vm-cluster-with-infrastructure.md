---
title: "Hozzon létre egy Virtuálisgép-fürt Terraform és Hardverkompatibilitási"
description: "Terraform és HashiCorp konfigurációs nyelvi (Hardverkompatibilitási) segítségével hozzon létre egy Linux virtuális gép fürtöt egy terhelés-kiegyenlítő az Azure-ban"
keywords: "terraform, a devops, a virtuális gépet, a hálózati, a modulok"
author: tomarcher
manager: routlaw
ms.service: virtual-machines-linux
ms.custom: devops
ms.topic: article
ms.date: 11/09/2017
ms.author: tarcher
ms.openlocfilehash: 859c0a2f2cf315e9168ed9828061c03da6b8e0a4
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2017
---
# <a name="create-a-vm-cluster-with-terraform-and-hcl"></a>Hozzon létre egy Virtuálisgép-fürt Terraform és Hardverkompatibilitási

Ez az oktatóanyag azt mutatja be, létrehozása egy kis számítási fürt használata a [HashiCorp konfigurációs nyelvi](https://www.terraform.io/docs/configuration/syntax.html) (Hardverkompatibilitási). A konfigurációs létrehoz egy terhelés-kiegyenlítő, két Linux virtuális gépek által egy [rendelkezésre állási csoport](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy), és az összes szükséges hálózati erőforrásokhoz.

Ebben az oktatóanyagban az alábbiakat végezte el:

> [!div class="checklist"]
> * Az Azure authentication beállítása
> * Egy Terraform konfigurációs fájl létrehozása
> * Terraform inicializálása
> * Terraform végrehajtási terv létrehozása
> * A Terraform végrehajtási terv alkalmazása

## <a name="1-set-up-azure-authentication"></a>1. Az Azure authentication beállítása

> [!NOTE]
> Ha meg [Terraform környezeti változókkal](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables), vagy futtassa az oktatóanyag a [Azure Cloud rendszerhéj](terraform-cloud-shell.md), hagyja ki ezt a szakaszt.

Ebben a szakaszban létrehozhat egy egyszerű Azure szolgáltatás, és két Terraform konfigurációs fájlokat tartalmazó a hitelesítő adatait a rendszerbiztonsági tag.

1. [Állítsa be az Azure AD szolgáltatás egyszerű](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure) Terraform az Azure erőforrások kiépítése lehetővé teszi, hogy. A rendszerbiztonsági tag létrehozásakor jegyezze fel az értékek az előfizetés-azonosító, a bérlő azonosítója, a displayName és a jelszót.

2. Nyisson meg egy parancssort.

3. Hozzon létre egy üres könyvtárat a Terraform fájlok tárolására.

4. Hozzon létre egy új fájlt, amely tárolja a változók deklarációja. Ezt a fájlt a tetszés bármilyen is nevet egy `.tf` bővítmény.

5. Másolja az alábbi kódot a táblaváltozó deklarációjából fájlba:

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

6. Hozzon létre egy új fájlt, amely tartalmazza a Terraform változók értékeit. Általános nevezze el a Terraform változó fájlt a `terraform.tfvars` Terraform automatikusan betöltése nevű `terraform.tfvars` (vagy egy mintát a következő `*.auto.tfvars`) Ha az aktuális könyvtárban található. 

7. Másolja az alábbi kódot a változók fájlba. Cserélje le a helyőrzőket az alábbiak szerint: A `subscription_id`, használja az Azure-előfizetése Azonosítóját futtatásakor megadott `az account set`. A `tenant_id`, használja a `tenant` által visszaadott érték `az ad sp create-for-rbac`. A `client_id`, használja a `appId` által visszaadott érték `az ad sp create-for-rbac`. A `client_secret`, használja a `password` által visszaadott érték `az ad sp create-for-rbac`.

  ```tf
  subscription_id = "<azure-subscription-id>"
  tenant_id = "<tenant-returned-from-creating-a-service-principal>"
  client_id = "<appId-returned-from-creating-a-service-principal>"
  client_secret = "<password-returned-from-creating-a-service-principal>"
  ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Egy Terraform konfigurációs fájl létrehozása

Ebben a szakaszban egy erőforrás-definíciókban a infrastruktúra tartalmazó fájlt hoz létre.

1. Hozzon létre egy új fájlt `main.tf`. 

2. Erőforrás-definíciókban minta az újonnan létrehozott be a következő másolási `main.tf` fájlt: 

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

## <a name="3-initialize-terraform"></a>3. Terraform inicializálása 

A [terraform init parancs](https://www.terraform.io/docs/commands/init.html) inicializáló Terraform konfigurációs fájlokat – a segítségével a korábbi szakaszokban létrehozott fájlokat tartalmazó könyvtár. Mindig fusson a `terraform init` parancsot egy új Terraform konfigurációs írását követően. 

> [!TIP]
> A `terraform init` parancs az idempotent, ami azt jelenti, hogy hívhatják ismételten ugyanazt az eredményt elkészítése közben. Ezért együttműködve dolgozik, és úgy gondolja, hogy előfordulhat, hogy a konfigurációs fájlok megváltoztak, esetén mindig célszerű hívni a `terraform init` parancs végrehajtása vagy a terv alkalmazása előtt.

Terraform inicializálni a következő parancsot:

  ```cmd
  terraform init
  ```

  ![Terraform inicializálása](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Terraform végrehajtási terv létrehozása

A [terraform terv parancs](https://www.terraform.io/docs/commands/plan.html) egy végrehajtási terv létrehozására szolgál. Egy végrehajtási terv létrehozásához Terraform összesíti az összes a `.tf` az aktuális könyvtárban található fájlokat. 

Ha egy együttműködési környezetben, ahol a konfigurációt a végrehajtási terv létrehozásához és az idő között változhat dolgozik a végrehajtási terv alkalmaz, használja a [terraform terv parancs-kimeneti paraméter](https://www.terraform.io/docs/commands/plan.html#out-path)és a kimeneti fájlba a végrehajtási terv. Ellenkező esetben ha egyetlen-személy környezetben dolgozik, akkor kihagyhatja a `-out` paraméter.

Ha a Terraform változók fájl neve nincs `terraform.tfvars` és azt nem követi a `*.auto.tfvars` mintát, meg kell adnia a fájl nevét használja a [terraform terv parancs - var-fájl paraméter](https://www.terraform.io/docs/commands/plan.html#var-file-foo) a jelentés futtatásakor a `terraform plan`parancsot.

Feldolgozásakor a `terraform plan` parancs Terraform hajtja végre a frissítést, és határozza meg, milyen műveletek szükségesek a kívánt állapot konfigurációs fájljainak megadott eléréséhez.

Ha nem szeretné menteni a végrehajtási terv, futtassa a következő parancsot:

  ```cmd
  terraform plan
  ```

Ha mentenie kell a végrehajtási terv, futtassa a következő parancsot (cseréje a &lt;elérési útja > helyőrzőt a kívánt kimeneti elérési út):

  ```cmd
  terraform plan -out=<path>
  ```

![Terraform végrehajtási terv létrehozása](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. A Terraform végrehajtási terv alkalmazása

Ez az oktatóanyag utolsó lépésében, hogy használja a [terraform alkalmazása parancs](https://www.terraform.io/docs/commands/apply.html) alkalmazza a által generált műveletek a `terraform plan` parancsot.

Ha szeretné alkalmazni a legújabb végrehajtási terv, futtassa a következő parancsot:

  ```cmd
  terraform apply
  ```

Ha egy korábban mentett végrehajtási terv alkalmazni kívánt, futtassa a következő parancsot (cseréje a &lt;elérési útja > elérési útvonalának a mentett végrehajtási terv helyőrzőt):

  ```cmd
  terraform apply <path>
  ```

![Egy Terraform végrehajtási terv alkalmazása](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Következő lépések

- Keresse meg a listában a [Azure Terraform modulok](https://registry.terraform.io/modules/Azure)
- Hozzon létre egy [virtuálisgép-méretezési rendelkező Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)