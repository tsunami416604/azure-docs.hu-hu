---
title: Oktatóanyag – Application Gateway bejövő adatkezelő létrehozása az Azure Kubernetes szolgáltatásban
description: Az oktatóanyag bemutatja, hogyan hozhat létre Kubernetes-fürtöt az Azure Kubernetes szolgáltatással a bejövő adatforgalom-vezérlővel Application Gateway
ms.topic: tutorial
ms.date: 11/13/2019
ms.openlocfilehash: b16b0a40d14ecde87b2637976299d05d37d706f3
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472264"
---
# <a name="tutorial-create-an-application-gateway-ingress-controller-in-azure-kubernetes-service"></a>Oktatóanyag: Application Gateway bejövő adatkezelő létrehozása az Azure Kubernetes szolgáltatásban

Az [Azure Kubernetes Service (ak)](/azure/aks/) kezeli az üzemeltetett Kubernetes-környezetet. Az AK használatával gyorsan és egyszerűen helyezhet üzembe és kezelhet tároló alkalmazásokat a tároló-előkészítési szakértelem nélkül. Az AK azt is kiküszöböli, hogy az alkalmazások offline állapotba helyezése működési és karbantartási feladatok esetén is megtörténjen. Ezek a feladatok – többek között a kiépítéssel, a verziófrissítéssel és az erőforrások méretezésével – igény szerint is elvégezhetők.

A bejövő adatkezelők különböző funkciókat biztosítanak a Kubernetes-szolgáltatásokhoz. Ezek a szolgáltatások közé tartozik a fordított proxy, a konfigurálható forgalom útválasztása és a TLS-lezárás. A Kubernetes bejövő erőforrásai az egyes Kubernetes-szolgáltatások bejövő szabályainak konfigurálására szolgálnak. A bejövő és a bejövő forgalomra vonatkozó szabályok használatával egyetlen IP-cím is átirányíthatja a forgalmat több szolgáltatásba egy Kubernetes-fürtben. Ezt a funkciót az Azure [Application Gateway](/azure/Application-Gateway/)biztosítja, így ideális bejövő Kubernetes az Azure-ban. 

Ebből az oktatóanyagból megtudhatja, hogyan hajthatja végre a következő feladatokat:

> [!div class="checklist"]
> * Hozzon létre egy [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) -fürtöt az AK-val Application Gateway bejövő vezérlőként.
> * A HCL (HashiCorp Language) használatával Definiáljon egy Kubernetes-fürtöt.
> * Application Gateway erőforrás létrehozásához használja a Terraform.
> * Hozzon létre egy Kubernetes-fürtöt a Terraform és az AK használatával.
> * A Kubernetes-fürt rendelkezésre állásának teszteléséhez használja a kubectl eszközt.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **A Terraform konfigurálása**: Kövesse a [Terraform telepítését és az Azure-hozzáférés konfigurálását ismertető cikkben](terraform-install-configure.md) található utasításokat

- **Azure-erőforráscsoport**: Ha nem rendelkezik a bemutatóhoz használni kívánt Azure-erőforráscsoporthoz, [hozzon létre egy Azure-erőforráscsoportot](/azure/azure-resource-manager/manage-resource-groups-portal#create-resource-groups). Jegyezze fel az erőforráscsoport nevét és helyét, mivel ezek az értékek a bemutatóban szerepelnek.

- **Azure-beli szolgáltatásnév**: Kövesse az **Azure-beli szolgáltatásnév létrehozása az Azure CLI-vel** című cikk [a szolgáltatásnév létrehozását](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) ismertető szakaszában foglaltakat. Jegyezze fel a appId, a displayName és a jelszó értékeit.

- Adja meg **az egyszerű szolgáltatásnév objektum azonosítóját**: futtassa a következő parancsot a Cloud Shellban: `az ad sp list --display-name <displayName>`

## <a name="create-the-directory-structure"></a>A könyvtárstruktúra létrehozása

Az első lépés a könyvtár létrehozása, amely a feladathoz tartozó Terraform konfigurációs fájlokat tárolja.

1. Keresse fel az [Azure Portalt](https://portal.azure.com).

1. Nyissa meg az [Azure Cloud Shellt](/azure/cloud-shell/overview).

1. Lépjen be a `clouddrive` könyvtárba.

    ```bash
    cd clouddrive
    ```

1. Hozzon létre egy `terraform-aks-appgw-ingress` nevű könyvtárat.

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
    code main.tf
    ```

1. Másolja az alábbi kódot a szerkesztőbe:

    ```hcl
    provider "azurerm" {
        version = "~>1.18"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Mentse a fájlt ( **&lt;ctrl > S**), és lépjen ki a szerkesztőből ( **&lt;CTRL > Q**).

## <a name="define-input-variables"></a>Bemeneti változók definiálása

Hozza létre a Terraform konfigurációs fájlját, amely felsorolja a telepítéshez szükséges összes változót.

1. Hozzon létre egy `variables.tf` nevű fájlt a Cloud Shellben.

    ```bash
    code variables.tf
    ```

1. Másolja az alábbi kódot a szerkesztőbe:
    
    ```hcl
    variable "resource_group_name" {
      description = "Name of the resource group."
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
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 applies the default disk size for that agentVMSize."
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

1. Mentse a fájlt ( **&lt;ctrl > S**), és lépjen ki a szerkesztőből ( **&lt;CTRL > Q**).

## <a name="define-the-resources"></a>Erőforrások definiálása 
Hozzon létre egy Terraform-konfigurációs fájlt, amely létrehozza az összes erőforrást. 

1. Hozzon létre egy `resources.tf` nevű fájlt a Cloud Shellben.

    ```bash
    code resources.tf
    ```

1. Illessze be a következő kódrészletet egy helyi blokk létrehozásához az újból felhasználható számított változókhoz:

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

1. Illessze be a következő kódrészletet egy adatforrásnak az erőforráscsoporthoz való létrehozásához, új felhasználói identitás:

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = var.resource_group_name
    }

    # User Assigned Identities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      name = "identity1"

      tags = var.tags
    }
    ```

1. Illessze be a következő kódrészletet az alapszintű hálózati erőforrások létrehozásához:

    ```hcl
    resource "azurerm_virtual_network" "test" {
      name                = var.virtual_network_name
      location            = data.azurerm_resource_group.rg.location
      resource_group_name = data.azurerm_resource_group.rg.name
      address_space       = [var.virtual_network_address_prefix]

      subnet {
        name           = var.aks_subnet_name
        address_prefix = var.aks_subnet_address_prefix
      }

      subnet {
        name           = "appgwsubnet"
        address_prefix = var.app_gateway_subnet_address_prefix
      }

      tags = var.tags
    }

    data "azurerm_subnet" "kubesubnet" {
      name                 = var.aks_subnet_name
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = data.azurerm_resource_group.rg.location
      resource_group_name          = data.azurerm_resource_group.rg.name
      allocation_method            = "Static"
      sku                          = "Standard"

      tags = var.tags
    }
    ```

1. Illessze be a következő kódrészletet Application Gateway erőforrás létrehozásához:

    ```hcl
    resource "azurerm_application_gateway" "network" {
      name                = var.app_gateway_name
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      sku {
        name     = var.app_gateway_sku
        tier     = "Standard_v2"
        capacity = 2
      }

      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = data.azurerm_subnet.appgwsubnet.id
      }

      frontend_port {
        name = local.frontend_port_name
        port = 80
      }

      frontend_port {
        name = "httpsPort"
        port = 443
      }

      frontend_ip_configuration {
        name                 = local.frontend_ip_configuration_name
        public_ip_address_id = azurerm_public_ip.test.id
      }

      backend_address_pool {
        name = local.backend_address_pool_name
      }

      backend_http_settings {
        name                  = local.http_setting_name
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }

      http_listener {
        name                           = local.listener_name
        frontend_ip_configuration_name = local.frontend_ip_configuration_name
        frontend_port_name             = local.frontend_port_name
        protocol                       = "Http"
      }

      request_routing_rule {
        name                       = local.request_routing_rule_name
        rule_type                  = "Basic"
        http_listener_name         = local.listener_name
        backend_address_pool_name  = local.backend_address_pool_name
        backend_http_settings_name = local.http_setting_name
      }

      tags = var.tags

      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```

1. A szerepkör-hozzárendelések létrehozásához illessze be a következő kódrészletet:

    ```hcl
    resource "azurerm_role_assignment" "ra1" {
      scope                = data.azurerm_subnet.kubesubnet.id
      role_definition_name = "Network Contributor"
      principal_id         = var.aks_service_principal_object_id 

      depends_on = ["azurerm_virtual_network.test"]
    }

    resource "azurerm_role_assignment" "ra2" {
      scope                = azurerm_user_assigned_identity.testIdentity.id
      role_definition_name = "Managed Identity Operator"
      principal_id         = var.aks_service_principal_object_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }

    resource "azurerm_role_assignment" "ra3" {
      scope                = azurerm_application_gateway.network.id
      role_definition_name = "Contributor"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }

    resource "azurerm_role_assignment" "ra4" {
      scope                = data.azurerm_resource_group.rg.id
      role_definition_name = "Reader"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```

1. Illessze be a következő kódrészletet a Kubernetes-fürt létrehozásához:

    ```hcl
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = var.aks_name
      location   = data.azurerm_resource_group.rg.location
      dns_prefix = var.aks_dns_prefix

      resource_group_name = data.azurerm_resource_group.rg.name

      linux_profile {
        admin_username = var.vm_user_name

        ssh_key {
          key_data = file(var.public_ssh_key_path)
        }
      }

      addon_profile {
        http_application_routing {
          enabled = false
        }
      }

      agent_pool_profile {
        name            = "agentpool"
        count           = var.aks_agent_count
        vm_size         = var.aks_agent_vm_size
        os_type         = "Linux"
        os_disk_size_gb = var.aks_agent_os_disk_size
        vnet_subnet_id  = data.azurerm_subnet.kubesubnet.id
      }

      service_principal {
        client_id     = var.aks_service_principal_app_id
        client_secret = var.aks_service_principal_client_secret
      }

      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = var.aks_dns_service_ip
        docker_bridge_cidr = var.aks_docker_bridge_cidr
        service_cidr       = var.aks_service_cidr
      }

      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = var.tags
    }

    ```

1. Mentse a fájlt ( **&lt;ctrl > S**), és lépjen ki a szerkesztőből ( **&lt;CTRL > Q**).

Az ebben a szakaszban bemutatott kód a fürt, a hely és a resource_group_name nevét állítja be. A `dns_prefix` érték – a fürthöz való hozzáféréshez használt teljes tartománynév (FQDN) részét képezi.

A `linux_profile` rekord lehetővé teszi, hogy konfigurálja azokat a beállításokat, amelyek engedélyezik az SSH-val való bejelentkezést a munkavégző csomópontokra.

Az AKS-sel csak a munkavégző csomópontokért kell fizetnie. A `agent_pool_profile` rekord a munkavégző csomópontok adatait konfigurálja. A `agent_pool_profile record` tartalmazza a létrehozandó munkavégző csomópontok számát és a feldolgozó csomópontok típusát. Ha a későbbiekben a fürt vertikális felskálázását vagy leskálázását szeretné végezni, módosítsa a rekord `count` értékét.

## <a name="create-a-terraform-output-file"></a>Terraform kimeneti fájl létrehozása

A [Terraform kimenetei](https://www.terraform.io/docs/configuration/outputs.html) lehetővé teszik a felhasználó számára kiemelt értékek meghatározását, amikor a Terraform alkalmazza a tervet, és a `terraform output` parancs használatával kérdezhető le. Ebben a szakaszban létrehozza a kimeneti fájlt, amellyel hozzáférhet a fürthöz a [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) használatával.

1. Hozzon létre egy `output.tf` nevű fájlt a Cloud Shellben.

    ```bash
    code output.tf
    ```

1. Másolja az alábbi kódot a szerkesztőbe:

    ```hcl
    output "client_key" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_key
    }

    output "client_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate
    }

    output "cluster_ca_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate
    }

    output "cluster_username" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.username
    }

    output "cluster_password" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.password
    }

    output "kube_config" {
        value = azurerm_kubernetes_cluster.k8s.kube_config_raw
    }

    output "host" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.host
    }

    output "identity_resource_id" {
        value = azurerm_user_assigned_identity.testIdentity.id
    }

    output "identity_client_id" {
        value = azurerm_user_assigned_identity.testIdentity.client_id
    }
    ```

1. Mentse a fájlt ( **&lt;ctrl > S**), és lépjen ki a szerkesztőből ( **&lt;CTRL > Q**).

## <a name="configure-azure-storage-to-store-terraform-state"></a>Az Azure Storage konfigurálása a Terraform állapotának tárolásához

A Terraform helyileg követi nyomon az állapotot a `terraform.tfstate` fájlon keresztül. Ez a minta jól működik egy egyszemélyes környezetben. A többszemélyesebb környezetekben azonban a kiszolgálón az [Azure Storage](/azure/storage/)használatával kell nyomon követnie az állapotot. Ebben a szakaszban megtudhatja, hogyan kérheti le a szükséges Storage-fiók adatait, és hogyan hozhat létre egy tárolót. Ekkor a rendszer a tárolóban tárolja a Terraform állapotinformációkat.

1. A Azure Portal az Azure- **szolgáltatások**területen válassza a **Storage-fiókok**lehetőséget. (Ha a **Storage-fiókok** beállítás nem látható a főoldalon, válassza a **További szolgáltatások** elemet, majd keresse meg és jelölje ki.)

1. A **Storage-fiókok** lapon válassza ki annak a Storage-fióknak a nevét, amelybe a Terraform az állapotot tárolni szeretné. Használhatja például azt a tárfiókot is, amely a Cloud Shell első megnyitásakor jött létre.  A Cloud Shell által létrehozott tárfiók neve általában `cs` értékkel kezdődik, amelyet számok és betűk véletlenszerű sorozata követ. 

    Jegyezze fel a kiválasztott Storage-fiókot, mert később szüksége lesz rá.

1. A Tárfiók oldalon válassza a **Hozzáférési kulcsok** elemet.

    ![Tárfiók menüje](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Jegyezze fel a **key1** **kulcs** értékét. (A kulcs jobb oldalán található ikonra kattintva a vágólapra másolhatja az értéket.)

    ![Tárfiók hozzáférési kulcsa](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. A Cloud Shell-ban hozzon létre egy tárolót az Azure Storage-fiókban. Cserélje le a helyőrzőket az Azure Storage-fiókja megfelelő értékeire.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Kubernetes-fürt létrehozása
Ez a szakasz ismerteti, hogyan használható a `terraform init` parancs az előző szakaszokban létrehozott konfigurációs fájlokat meghatározó erőforrások létrehozásához.

1. A Cloud Shell-ben inicializálja a Terraform. Cserélje le a helyőrzőket az Azure Storage-fiókja megfelelő értékeire.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
  
    A `terraform init` parancs a háttérrendszer és a szolgáltató beépülő modul inicializálásának sikerességét jeleníti meg:

    ![A „terraform init” eredményeit mutató példa](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. Cloud Shell hozzon létre egy `terraform.tfvars`nevű fájlt:

    ```bash
    code terraform.tfvars
    ```

1. Illessze be a korábban létrehozott következő változókat a szerkesztőbe. A környezet helyének értékének beszerzéséhez használja a `az account list-locations`.

    ```hcl
    resource_group_name = "<Name of the Resource Group already created>"

    location = "<Location of the Resource Group>"
      
    aks_service_principal_app_id = "<Service Principal AppId>"
      
    aks_service_principal_client_secret = "<Service Principal Client Secret>"
      
    aks_service_principal_object_id = "<Service Principal Object Id>"
        
    ```

1. Mentse a fájlt ( **&lt;ctrl > S**), és lépjen ki a szerkesztőből ( **&lt;CTRL > Q**).

1. Futtassa a `terraform plan` parancsot az infrastruktúra elemeit meghatározó Terraform-terv létrehozásához. 

    ```bash
    terraform plan -out out.plan
    ```

    A `terraform plan` parancs megjeleníti a `terraform apply` parancs futtatásakor létrehozott erőforrásokat:

    ![A „terraform plan” eredményeit mutató példa](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Futtassa a `terraform apply` parancsot a Kubernetes-fürtöt létrehozó terv alkalmazásához. A Kubernetes-fürt létrehozásának folyamata több percet is igénybe vehet, ami a Cloud Shell munkamenet időtúllépését eredményezi. Ha a Cloud Shell munkamenet időtúllépés miatt meghaladta az időkorlátot, kövesse a "helyreállítás Cloud Shell időtúllépésből" című szakasz lépéseit az oktatóanyag befejezéséhez.

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

1. Futtassa az alábbi parancsot:

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

## <a name="install-azure-ad-pod-identity"></a>Az Azure AD Pod-identitás telepítése

Azure Active Directory Pod Identity jogkivonat-alapú hozzáférést biztosít [Azure Resource Managerhoz](/azure/azure-resource-manager/resource-group-overview).

Az [Azure ad Pod Identity](https://github.com/Azure/aad-pod-identity) a következő összetevőket adja hozzá a Kubernetes-fürthöz:

  - Kubernetes [CRDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity`, `AzureAssignedIdentity`, `AzureIdentityBinding`
  - [Felügyelt identitás-vezérlő (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic) összetevő
  - [Csomópont által felügyelt identitás (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi) összetevő

Ha a RBAC **engedélyezve**van, futtassa a következő parancsot az Azure ad Pod Identity telepítéséhez a fürtön:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
```

Ha a RBAC **le van tiltva**, futtassa a következő parancsot az Azure ad Pod Identity telepítéséhez a fürtön:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
```

## <a name="install-helm"></a>A Helm telepítése

Az ebben a szakaszban szereplő kód a [Helm](/azure/aks/kubernetes-helm) -Kubernetes csomagkezelő használatával telepíti a `application-gateway-kubernetes-ingress` csomagot:

1. Ha a RBAC **engedélyezve**van, futtassa a következő parancsokat a Helm telepítéséhez és konfigurálásához:

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

1. Ha a RBAC **le van tiltva**, futtassa a következő parancsot a Helm telepítéséhez és konfigurálásához:

    ```bash
    helm init
    ```

1. Adja hozzá a AGIC Helm-tárházat:

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="install-ingress-controller-helm-chart"></a>A beáramló vezérlő Helm-diagramjának telepítése

1. `helm-config.yaml` letöltése a AGIC konfigurálásához:

    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```

1. Szerkessze a `helm-config.yaml`, és adja meg a megfelelő értékeket `appgw` és `armAuth` szakaszban.

    ```bash
    code helm-config.yaml
    ```

    Az értékek a következőképpen vannak leírva:

    - `verbosityLevel`: a AGIC naplózási infrastruktúrájának részletességi szintjét állítja be. Lásd: a lehetséges értékek [naplózási szintjei](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) .
    - `appgw.subscriptionId`: az App Gateway Azure-előfizetés azonosítója. Például: `a123b234-a3b4-557d-b2df-a0bc12de1234`
    - `appgw.resourceGroup`: az az Azure-erőforráscsoport neve, amelyben az App Gateway létrejött. 
    - `appgw.name`: a Application Gateway neve. Példa: `applicationgateway1`.
    - `appgw.shared`: ezt a logikai jelzőt alapértelmezett értékre kell állítani a `false`. A `true` beállításnál a [megosztott alkalmazás-átjáróra](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)van szükség.
    - `kubernetes.watchNamespace`: adja meg azt a AGIC, amelynek a nevét meg kell nézni. A névtér lehet egy karakterlánc-érték, vagy a névterek vesszővel tagolt listája. Ha elhagyja ezt a változót, vagy üresre vagy üres karakterláncra állítja, akkor a bejövő adatforgalom-vezérlő az összes elérhető névteret megfigyelve.
    - `armAuth.type`: `aadPodIdentity` vagy `servicePrincipal`érték.
    - `armAuth.identityResourceID`: a felügyelt identitás erőforrás-azonosítója.
    - `armAuth.identityClientId`: az identitás ügyfél-azonosítója.
    - `armAuth.secretJSON`: csak akkor szükséges, ha a szolgáltatás egyszerű titkos típusa van kiválasztva (ha `armAuth.type` van beállítva a következőre: `servicePrincipal`).

    Fontos megjegyzések:
    - A `identityResourceID` érték a Terraform parancsfájlban jön létre, és a következő futtatásával érhető el: `echo "$(terraform output identity_resource_id)"`.
    - A `identityClientID` érték a Terraform parancsfájlban jön létre, és a következő futtatásával érhető el: `echo "$(terraform output identity_client_id)"`.
    - Az `<resource-group>` érték az App Gateway erőforráscsoport.
    - A `<identity-name>` érték a létrehozott identitás neve.
    - Az adott előfizetéshez tartozó összes identitás a következő használatával listázható: `az identity list`.

1. Telepítse a Application Gateway beáramló vezérlő csomagot:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

### <a name="install-a-sample-app"></a>Minta alkalmazás telepítése

Ha telepítette az App Gateway-t, az AK-t és a AGIC-t, a [Azure Cloud Shell](https://shell.azure.com/)használatával telepítheti a minta alkalmazást:

1. A YAML fájl letöltéséhez használja a cURL parancsot:

    ```bash
    curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
    ```

2. A YAML fájl alkalmazása:

    ```bash
    kubectl apply -f aspnetapp.yaml
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat.  

Cserélje le a helyőrzőt a megfelelő értékre. A megadott erőforráscsoport összes erőforrása törölve lesz.

```bash
az group delete -n <resource-group>
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Bejövő Application Gateway vezérlő](https://azure.github.io/application-gateway-kubernetes-ingress/)
