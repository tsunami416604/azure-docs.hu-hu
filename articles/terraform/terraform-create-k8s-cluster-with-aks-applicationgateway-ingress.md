---
title: Kubernetes-fürt létrehozása Application Gateway az Azure Kubernetes szolgáltatással (ak)
description: Az oktatóanyag bemutatja, hogyan hozhat létre Kubernetes-fürtöt az Azure Kubernetes szolgáltatással a bejövő adatforgalom-vezérlővel Application Gateway
services: terraform
ms.service: azure
keywords: Terraform, devops, virtuális gép, Azure, kubernetes, bejövő forgalom, Application Gateway
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 0373b254a900fd34232bb6863c93802fa7b51aab
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169964"
---
# <a name="create-a-kubernetes-cluster-with-application-gateway-ingress-controller-using-azure-kubernetes-service-and-terraform"></a>Kubernetes-fürt létrehozása Application Gateway beáramlási vezérlővel az Azure Kubernetes Service és a Terraform használatával
Az [Azure Kubernetes Service (ak)](/azure/aks/) kezeli az üzemeltetett Kubernetes-környezetet. Az AK használatával gyorsan és egyszerűen helyezhet üzembe és kezelhet tároló alkalmazásokat a tároló-előkészítési szakértelem nélkül. Ezenkívül a folyamatban lévő műveletek és karbantartás terhét is megszünteti az erőforrások igény szerinti kiépítésével, frissítésével és méretezésével anélkül, hogy offline állapotba kellene helyezni az alkalmazásait.

A bejövő vezérlő egy olyan szoftver, amely fordított proxyt, konfigurálható forgalmi útválasztást és TLS-megszakítást biztosít a Kubernetes-szolgáltatásokhoz. A Kubernetes bejövő erőforrásai az egyes Kubernetes-szolgáltatások bejövő szabályainak és útvonalának konfigurálására szolgálnak. A bejövő és a bejövő forgalomra vonatkozó szabályok használatával egyetlen IP-cím segítségével átirányíthatja a forgalmat több szolgáltatásba egy Kubernetes-fürtben. Az Azure [Application Gateway](/azure/Application-Gateway/)az összes fenti funkciót elérhetővé teszi, ami ideális bejövő Kubernetes biztosít az Azure-ban. 

Ebből az oktatóanyagból megtudhatja, hogyan végezheti el a következő feladatokat a [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) -FÜRTÖK az AK-val való létrehozásával Application Gateway bejövő vezérlőként:

> [!div class="checklist"]
> * Kubernetes-fürt meghatározása HCL (HashiCorp Language) használatával
> * Application Gateway erőforrás létrehozása a Terraform használatával
> * Kubernetes-fürt létrehozása Terraformmal és AKS-sel
> * Kubernetes-fürt rendelkezésre állásának tesztelése a kubectl eszközzel

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.

- **Terraform konfigurálása**: Kövesse a cikk utasításait, [Terraform és konfigurálja az Azure-hoz való hozzáférést](/azure/virtual-machines/linux/terraform-install-configure)

- **Azure egyszerű szolgáltatás**: Kövesse a cikk **egyszerű szolgáltatásnév létrehozása** című szakaszának utasításait, és [hozzon létre egy Azure-SZOLGÁLTATÁSNEVET az Azure CLI-vel](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Jegyezze fel a appId, a displayName és a jelszó értékeit.
  - Jegyezze fel az egyszerű szolgáltatásnév objektum-AZONOSÍTÓját a következő parancs futtatásával

    ```azurecli
    az ad sp list --display-name <displayName>
    ```

## <a name="create-the-directory-structure"></a>A könyvtárstruktúra létrehozása
Az első lépés a könyvtár létrehozása, amely a feladathoz tartozó Terraform konfigurációs fájlokat tárolja.

1. Keresse fel az [Azure Portalt](https://portal.azure.com).

1. Nyissa meg az [Azure Cloud Shellt](/azure/cloud-shell/overview). Ha még nem választott ki környezetet, válassza a **Bash** környezetet.

    ![Cloud Shell-parancssor](./media/terraform-k8s-cluster-appgw-with-tf-aks/azure-portal-cloud-shell-button-min.png)

1. Lépjen be a `clouddrive` könyvtárba.

    ```bash
    cd clouddrive
    ```

1. Hozzon létre egy `terraform-aks-k8s` nevű könyvtárat.

    ```bash
    mkdir terraform-aks-appgw-ingress
    ```

1. Lépjen be az új könyvtárba:

    ```bash
    cd terraform-aks-appgw-ingress
    ```

## <a name="declare-the-azure-provider"></a>Az Azure-szolgáltató deklarálása
Hozza létre az Azure-szolgáltatót deklaráló Terraform konfigurációs fájlt.

1. Hozzon létre egy `main.tf` nevű fájlt a Cloud Shellben.

    ```bash
    vi main.tf
    ```

1. Az I billentyű lenyomásával lépjen beszúrási módba.

1. Másolja az alábbi kódot a szerkesztőbe:

    ```hcl
    provider "azurerm" {
        version = "~>1.18"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. A beszúrás módból az **Esc** billentyűvel léphet ki.

1. Mentse a fájlt, és lépjen ki a VI-szerkesztőből a következő parancs megadásával:

    ```bash
    :wq
    ```

## <a name="define-input-variables"></a>Bemeneti változók definiálása
Hozza létre a Terraform konfigurációs fájlját, amely felsorolja a telepítéshez szükséges összes változót.

1. Hozzon létre egy `variables.tf` nevű fájlt a Cloud Shellben.

    ```bash
    vi variables.tf
    ```

1. Az I billentyű lenyomásával lépjen beszúrási módba.

1. Másolja az alábbi kódot a szerkesztőbe:
    
    ```hcl
    variable "resource_group_name" {
      description = "Name of the resource group already created."
    }
    
    variable "location" {
      description = "Location of the cluster."
    }
    
    variable "aks_service_principal_app_id" {
      description = "Application ID/Client ID  of the service principal. Used by AKS to manage AKS related resources on Azure like vms, subnets."
    }
    
    variable "aks_service_principal_client_secret" {
      description = "Secret of the service principal. Used by AKS to manage Azure."
    }
    
    variable "aks_service_principal_object_id" {
      description = "Object ID of the service principal."
    }
    
    variable "virtual_network_name" {
      description = "Virtual network name"
      default     = "aksVirtualNetwork"
    }
    
    variable "virtual_network_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/8"
    }
    
    variable "aks_subnet_name" {
      description = "AKS Subnet Name."
      default     = "kubesubnet"
    }
    
    variable "aks_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/16"
    }
    
    variable "app_gateway_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.1.0.0/16"
    }
    
    variable "app_gateway_name" {
      description = "Name of the Application Gateway."
      default = "ApplicationGateway1"
    }
    
    variable "app_gateway_sku" {
      description = "Name of the Application Gateway SKU."
      default = "Standard_v2"
    }
    
    
    variable "app_gateway_tier" {
      description = "Tier of the Application Gateway SKU."
      default = "Standard_v2"
    }
    
    
    variable "aks_name" {
      description = "Name of the AKS cluster."
      default     = "aks-cluster1"
    }
    variable "aks_dns_prefix" {
      description = "Optional DNS prefix to use with hosted Kubernetes API server FQDN."
      default     = "aks"
    }
    
    
    variable "aks_agent_os_disk_size" {
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 will apply the default disk size for that agentVMSize."
      default     = 40
    }
    
    variable "aks_agent_count" {
      description = "The number of agent nodes for the cluster."
      default     = 3
    }
    
    variable "aks_agent_vm_size" {
      description = "The size of the Virtual Machine."
      default     = "Standard_D3_v2"
    }
    
    variable "kubernetes_version" {
      description = "The version of Kubernetes."
      default     = "1.11.5"
    }
    
    variable "aks_service_cidr" {
      description = "A CIDR notation IP range from which to assign service cluster IPs."
      default     = "10.0.0.0/16"
    }
    
    variable "aks_dns_service_ip" {
      description = "Containers DNS server IP address."
      default     = "10.0.0.10"
    }
    
    variable "aks_docker_bridge_cidr" {
      description = "A CIDR notation IP for Docker bridge."
      default     = "172.17.0.1/16"
    }
    
    variable "aks_enable_rbac" {
      description = "Enable RBAC on the AKS cluster. Defaults to false."
      default     = "false"
    }
    
    variable "vm_user_name" {
      description = "User name for the VM"
      default     = "vmuser1"
    }
    
    variable "public_ssh_key_path" {
      description = "Public key path for SSH."
      default     = "~/.ssh/id_rsa.pub"
    }
    
    variable "tags" {
      type = "map"
    
      default = {
        source = "terraform"
      }
    }
    ```

## <a name="define-the-resources"></a>Erőforrások definiálása 
Hozzon létre egy Terraform-konfigurációs fájlt, amely létrehozza az összes erőforrást. 

1. Hozzon létre egy `resources.tf` nevű fájlt a Cloud Shellben.

    ```bash
    vi resources.tf
    ```

1. Az I billentyű lenyomásával lépjen beszúrási módba.

1. Illessze be a következő kódrészleteket a szerkesztőbe:

    a. Hozzon létre egy helyi blokkot a számított változók számára az újrafelhasználáshoz.

    ```hcl
    # # Locals block for hardcoded names. 
    locals {
        backend_address_pool_name      = "${azurerm_virtual_network.test.name}-beap"
        frontend_port_name             = "${azurerm_virtual_network.test.name}-feport"
        frontend_ip_configuration_name = "${azurerm_virtual_network.test.name}-feip"
        http_setting_name              = "${azurerm_virtual_network.test.name}-be-htst"
        listener_name                  = "${azurerm_virtual_network.test.name}-httplstn"
        request_routing_rule_name      = "${azurerm_virtual_network.test.name}-rqrt"
        app_gateway_subnet_name = "appgwsubnet"
    }
    ```

    b. Hozzon létre egy adatforrást az erőforráscsoport számára, új felhasználói identitást.

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = "${var.resource_group_name}"
    }
    
    # User Assigned Idntities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"
    
      name = "identity1"
    
      tags = "${var.tags}"
    }
    ```

    c. Hozzon létre alapszintű hálózatkezelési erőforrásokat.

    ```hcl
    resource "azurerm_virtual_network" "test" {
      name                = "${var.virtual_network_name}"
      location            = "${data.azurerm_resource_group.rg.location}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      address_space       = ["${var.virtual_network_address_prefix}"]
    
      subnet {
        name           = "${var.aks_subnet_name}"
        address_prefix = "${var.aks_subnet_address_prefix}" 
      }
    
      subnet {
        name           = "appgwsubnet"
        address_prefix = "${var.app_gateway_subnet_address_prefix}"
      }
    
      tags = "${var.tags}"
    }
    
    data "azurerm_subnet" "kubesubnet" {
      name                 = "${var.aks_subnet_name}"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }
    
    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }
    
    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = "${data.azurerm_resource_group.rg.location}"
      resource_group_name          = "${data.azurerm_resource_group.rg.name}"
      public_ip_address_allocation = "static"
      sku                          = "Standard"
    
      tags = "${var.tags}"
    }
    ```

    d. Application Gateway erőforrás létrehozása.

    ```hcl
    resource "azurerm_application_gateway" "network" {
      name                = "${var.app_gateway_name}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"
    
      sku {
        name     = "${var.app_gateway_sku}"
        tier     = "Standard_v2"
        capacity = 2
      }
    
      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = "${data.azurerm_subnet.appgwsubnet.id}"
      }
    
      frontend_port {
        name = "${local.frontend_port_name}"
        port = 80
      }
    
      frontend_port {
        name = "httpsPort"
        port = 443
      }
    
      frontend_ip_configuration {
        name                 = "${local.frontend_ip_configuration_name}"
        public_ip_address_id = "${azurerm_public_ip.test.id}"
      }
    
      backend_address_pool {
        name = "${local.backend_address_pool_name}"
      }
    
      backend_http_settings {
        name                  = "${local.http_setting_name}"
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }
    
      http_listener {
        name                           = "${local.listener_name}"
        frontend_ip_configuration_name = "${local.frontend_ip_configuration_name}"
        frontend_port_name             = "${local.frontend_port_name}"
        protocol                       = "Http"
      }
    
      request_routing_rule {
        name                       = "${local.request_routing_rule_name}"
        rule_type                  = "Basic"
        http_listener_name         = "${local.listener_name}"
        backend_address_pool_name  = "${local.backend_address_pool_name}"
        backend_http_settings_name = "${local.http_setting_name}"
      }
    
      tags = "${var.tags}"
    
      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```

    e. Szerepkör-hozzárendelések létrehozása.

    ```hcl
    resource "azurerm_role_assignment" "ra1" {
      scope                = "${data.azurerm_subnet.kubesubnet.id}"
      role_definition_name = "Network Contributor"
      principal_id         = "${var.aks_service_principal_object_id }"
    
      depends_on = ["azurerm_virtual_network.test"]
    }
    
    resource "azurerm_role_assignment" "ra2" {
      scope                = "${azurerm_user_assigned_identity.testIdentity.id}"
      role_definition_name = "Managed Identity Operator"
      principal_id         = "${var.aks_service_principal_object_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }
    
    resource "azurerm_role_assignment" "ra3" {
      scope                = "${azurerm_application_gateway.network.id}"
      role_definition_name = "Contributor"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    
    resource "azurerm_role_assignment" "ra4" {
      scope                = "${data.azurerm_resource_group.rg.id}"
      role_definition_name = "Reader"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```

    f. Hozza létre a Kubernetes-fürtöt.

    ```hcl
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = "${var.aks_name}"
      location   = "${data.azurerm_resource_group.rg.location}"
      dns_prefix = "${var.aks_dns_prefix}"
    
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
    
      linux_profile {
        admin_username = "${var.vm_user_name}"
    
        ssh_key {
          key_data = "${file(var.public_ssh_key_path)}"
        }
      }
    
      addon_profile {
        http_application_routing {
          enabled = false
        }
      }
    
      agent_pool_profile {
        name            = "agentpool"
        count           = "${var.aks_agent_count}"
        vm_size         = "${var.aks_agent_vm_size}"
        os_type         = "Linux"
        os_disk_size_gb = "${var.aks_agent_os_disk_size}"
        vnet_subnet_id  = "${data.azurerm_subnet.kubesubnet.id}"
      }
    
      service_principal {
        client_id     = "${var.aks_service_principal_app_id}"
        client_secret = "${var.aks_service_principal_client_secret}"
      }
    
      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = "${var.aks_dns_service_ip}"
        docker_bridge_cidr = "${var.aks_docker_bridge_cidr}"
        service_cidr       = "${var.aks_service_cidr}"
      }
    
      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = "${var.tags}"
    }
    
    ```

    Az előző kód megadja a fürt nevét, helyét és az erőforráscsoport nevét. Emellett a dns_prefix érték is be lesz állítva. (Ez a fürt eléréséhez használt teljes tartománynév egy része.)

    A **linux_profile** rekord lehetővé teszi, hogy konfigurálja azokat a beállításokat, amelyek engedélyezik az SSH-bejelentkezést a munkavégző csomópontokra.

    Az AKS-sel csak a munkavégző csomópontokért kell fizetnie. Az **agent_pool_profile** rekord ezen munkavégző csomópontok részleteit konfigurálja. Az **agent_pool_profile record** tartalmazza a létrehozandó munkavégző csomópontok számát és a munkavégző csomópontok típusát. Ha a jövőben a fürt vertikális felskálázásra vagy leskálázásra lesz szükség, módosítsa a rekord **count** (darabszám) értékét.

1. A beszúrás módból az **Esc** billentyűvel léphet ki.

1. Mentse a fájlt, és lépjen ki a VI-szerkesztőből a következő parancs megadásával:

    ```bash
    :wq
    ```


## <a name="create-a-terraform-output-file"></a>Terraform kimeneti fájl létrehozása
A [Terraform-kimenetek](https://www.terraform.io/docs/configuration/outputs.html) lehetővé teszik, hogy megadja azokat az értékeket, amelyek ki lesznek emelve a felhasználó számára a Terraform-tervek alkalmazásakor, és lekérdezhetők a `terraform output` paranccsal. Ebben a szakaszban létrehozza a kimeneti fájlt, amellyel hozzáférhet a fürthöz a [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) használatával.

1. Hozzon létre egy `output.tf` nevű fájlt a Cloud Shellben.

    ```bash
    vi output.tf
    ```

1. Az I billentyű lenyomásával lépjen beszúrási módba.

1. Másolja az alábbi kódot a szerkesztőbe:

    ```hcl
    output "client_key" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_key}"
    }

    output "client_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate}"
    }

    output "cluster_ca_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate}"
    }

    output "cluster_username" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.username}"
    }

    output "cluster_password" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.password}"
    }

    output "kube_config" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config_raw}"
    }

    output "host" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.host}"
    }
    ```

1. A beszúrás módból az **Esc** billentyűvel léphet ki.

1. Mentse a fájlt, és lépjen ki a VI-szerkesztőből a következő parancs megadásával:

    ```bash
    :wq
    ```

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Az Azure Storage beállítása Terraform-állapot tárolásához
A Terraform helyileg követi nyomon az állapotot a `terraform.tfstate` fájlon keresztül. Ez a minta jól működik egy egyszemélyes környezetben. A többszemélyesebb környezetekben azonban a kiszolgálón az [Azure Storage](/azure/storage/)használatával kell nyomon követnie az állapotot. Ebben a szakaszban a Storage-fiók szükséges információit (a fióknevet és fiókkulcsot) kéri le, és létrehoz egy Storage-tárolót amelyben a Terraform állapotinformációit tárolja a rendszer.

1. Az Azure Portalon a bal oldali menüben válassza a **Minden szolgáltatás** elemet.

1. Válassza a **Tárfiókok** lehetőséget.

1. A **Tárfiókok** lapon válassza ki annak a tárfióknak nevét, amelyben a Terraform fogja tárolni az állapotot. Használhatja például azt a tárfiókot is, amely a Cloud Shell első megnyitásakor jött létre.  A Cloud Shell által létrehozott tárfiók neve általában `cs` értékkel kezdődik, amelyet számok és betűk véletlenszerű sorozata követ. **Jegyezze fel a kiválasztott Storage-fiók nevét, mert később szüksége lesz rá.**

1. A tárfiók lapon válassza a **Hozzáférési kulcsok** lehetőséget.

    ![Tárfiók menüje](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Jegyezze fel a **key1** **key** értékét. (A kulcs jobb oldalán található ikonra kattintva a vágólapra másolhatja az értéket.)

    ![Tárfiók hozzáférési kulcsa](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. A Cloud Shellben hozzon létre egy tárolót az Azure Storage-tárfiókban (cserélje le a &lt;YourAzureStorageAccountName> és a &lt;YourAzureStorageAccountAccessKey> helyőrzőket az Azure Storage-tárfiók megfelelő értékeire).

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Kubernetes-fürt létrehozása
Ez a szakasz ismerteti, hogyan használható a `terraform init` parancs az előző szakaszokban létrehozott konfigurációs fájlokat meghatározó erőforrások létrehozásához.

1. A Cloud Shellben inicializálja a Terraformot (cserélje le a &lt;YourAzureStorageAccountName> és a &lt;YourAzureStorageAccountAccessKey> helyőrzőket az Azure Storage-tárfiók megfelelő értékeire).

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    A `terraform init` parancs megjeleníti a háttérrendszer és a szolgáltató beépülő modul sikeres inicializálását:

    ![A „terraform init” eredményeit mutató példa](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. Hozzon létre egy változó fájlt, amely megadja a Cloud Shell bemeneti értékeit, `main.tf`hozzon létre egy nevű fájlt.

    ```bash
    vi terraform.tfvars
    ```

1. Az I billentyű lenyomásával lépjen beszúrási módba.

1. Illessze be a korábban létrehozott következő változókat a szerkesztőbe:

    ```hcl
      resource_group_name = <Name of the Resource Group already created>

      location = <Location of the Resource Group>
        
      aks_service_principal_app_id = <Service Principal AppId>
        
      aks_service_principal_client_secret = <Service Principal Client Secret>
        
      aks_service_principal_object_id = <Service Principal Object Id>
        
    ```

1. A beszúrás módból az **Esc** billentyűvel léphet ki.

1. Mentse a fájlt, és lépjen ki a VI-szerkesztőből a következő parancs megadásával:

    ```bash
    :wq
    ```

1. Futtassa a `terraform plan` parancsot az infrastruktúra elemeit meghatározó Terraform-terv létrehozásához. 

    ```bash
    terraform plan -out out.plan
    ```

    A `terraform plan` parancs megjeleníti azokat az erőforrásokat, amelyek a `terraform apply` parancs futtatásakor jönnek létre:

    ![A „terraform plan” eredményeit mutató példa](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Futtassa a `terraform apply` parancsot a Kubernetes-fürtöt létrehozó terv alkalmazásához. A Kubernetes-fürt létrehozásának folyamata néhány percet igénybe vehet, ez a Cloud Shell-munkamenet időtúllépését eredményezi. Ha a Cloud Shell munkamenet időtúllépés miatt meghaladta az időkorlátot, kövesse a "helyreállítás Cloud Shell időtúllépésből" című szakasz lépéseit az oktatóanyag befejezéséhez.

    ```bash
    terraform apply out.plan
    ```

    A `terraform apply` parancs megjeleníti a konfigurációs fájlokban meghatározott erőforrások létrehozásának eredményét:

    ![A „terraform apply” eredményeit mutató példa](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. A Azure Portal a bal oldali menüben válassza az **erőforráscsoportok** lehetőséget, hogy megtekintse az új Kubernetes-fürthöz létrehozott erőforrásokat a kiválasztott erőforráscsoporthoz.

    ![Cloud Shell-parancssor](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Helyreállítás a Cloud Shell időtúllépéséből
Ha a Cloud Shell munkamenet időtúllépést tapasztal, a következő lépésekkel állíthatja helyre a helyreállítást:

1. Indítson egy Cloud Shell-munkamenetet.

1. Lépjen a Terraform konfigurációs fájljait tartalmazó könyvtárra.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Futtassa a következő parancsot:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>A Kubernetes-fürt tesztelése
A Kubernetes-eszközök használhatók az újonnan létrehozott fürt teszteléséhez.

1. Kérje le a Kubernetes-konfigurációt a Terraform állapotából, és tárolja el egy fájlban, amelyet a kubectl olvashat.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Állítson be egy környezeti változót, így a kubectl a helyes konfigurációt veszi fel.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Ellenőrizze a fürt állapotát.

    ```bash
    kubectl get nodes
    ```

    Megjelenik a munkavégző csomópontok állapota, és minden csomópont **Ready** (Kész) állapotú, az alábbi képen láthatóhoz hasonlóan:

    ![A kubectl eszközzel ellenőrizheti a Kubernetes-fürt állapotát](./media/terraform-k8s-cluster-appgw-with-tf-aks/kubectl-get-nodes.png)


## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerte, hogyan használható a Terraform és az AKS egy Kubernetes-fürt létrehozásához. Íme néhány további erőforrás, amelyek segítenek többet megtudni az Azure-beli Terraform.
 
 > [!div class="nextstepaction"] 
 > [Terraform Hub a Microsoft.com webhelyen](https://docs.microsoft.com/azure/terraform/)
 
