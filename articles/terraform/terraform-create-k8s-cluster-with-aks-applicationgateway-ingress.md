---
title: Oktatóanyag – Hozzon létre egy alkalmazásátjáró bejövő forgalom vezérlőjét az Azure Kubernetes szolgáltatásban
description: Ebben az oktatóanyagban hozzon létre egy Kubernetes-fürtöt az Azure Kubernetes szolgáltatással az Application Gateway-rel bejövő forgalom vezérlőjeként
keywords: azure devops terraform alkalmazásátjáró be- és aks kubernetes
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 6b48d0acb654f0b0643c0754e53f6bc6ea76bb45
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945321"
---
# <a name="tutorial-create-an-application-gateway-ingress-controller-in-azure-kubernetes-service"></a>Oktatóanyag: Hozzon létre egy application gateway bejövő forgalom vezérlőt az Azure Kubernetes szolgáltatásban

[Az Azure Kubernetes-szolgáltatás (AKS)](/azure/aks/) kezeli a üzemeltetett Kubernetes-környezetet. Az AKS gyors és egyszerű üzembe helyezését és kezelését teszi a tárolóvezénylési szakértelem nélkül. Az AKS kiküszöböli az alkalmazások üzembe helyezése az üzemeltetési és karbantartási feladatok hozadékát is. Az AKS használatával ezek a feladatok – beleértve az erőforrások kiépítését, frissítését és méretezését – igény szerint elvégezhetők.

A be- éselőszoba-vezérlő különböző funkciókat biztosít a Kubernetes-szolgáltatásokhoz. Ezek közé tartozik a fordított proxy, a konfigurálható forgalomútválasztás és a TLS-végződtetés. Kubernetes inress erőforrások az egyes Kubernetes-szolgáltatások be- és hangrendszerszabályainak konfigurálására szolgálnak. Egy bejövő vezérlő és a bejövő forgalom szabályok használatával egyetlen IP-cím irányíthatja a forgalmat egy Kubernetes-fürt több szolgáltatásához. Mindezt a funkciót az Azure [Application Gateway](/azure/Application-Gateway/)biztosítja, így ideális ingress vezérlő az Azure-beli Kubernetes számára. 

Ebben az oktatóanyagban megtudhatja, hogyan kell elvégezni a következő feladatokat:

> [!div class="checklist"]
> * Hozzon létre egy [Kubernetes-fürtöt](https://www.redhat.com/en/topics/containers/what-is-kubernetes) az AKS-sel az Application Gateway-rel, mint a be- és élesztiirányító használatával.
> * A HCL (HashiCorp language) segítségével definiáljon egy Kubernetes-fürtöt.
> * A Terraform segítségével hozzon létre Alkalmazásátjáró-erőforrást.
> * A Terraform és az AKS segítségével hozzon létre egy Kubernetes-fürtöt.
> * A kubectl eszközzel tesztelje a Kubernetes-fürt rendelkezésre állását.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **A Terraform konfigurálása**: Kövesse a [Terraform telepítését és az Azure-hozzáférés konfigurálását ismertető cikkben](terraform-install-configure.md) található utasításokat

- **Azure-erőforráscsoport:** Ha nem rendelkezik azure-erőforráscsoporttal a bemutatóhoz, [hozzon létre egy Azure-erőforráscsoportot.](/azure/azure-resource-manager/manage-resource-groups-portal#create-resource-groups) Vegye figyelembe az erőforráscsoport nevét és helyét, mivel ezek az értékek a bemutatóban használatosak.

- **Azure-beli szolgáltatásnév**: Kövesse az [Azure-beli szolgáltatásnév létrehozása az Azure CLI-vel](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) című cikk **a szolgáltatásnév létrehozását** ismertető szakaszában foglaltakat. Vegye figyelembe az appId, displayName és jelszó értékeit.

- **Szerezze be az egyszerű szolgáltatásobjektum-azonosítót:** Futtassa a következő parancsot a Cloud Shell ben:`az ad sp list --display-name <displayName>`

## <a name="create-the-directory-structure"></a>A könyvtárstruktúra létrehozása

Az első lépés a könyvtár létrehozása, amely a feladathoz tartozó Terraform konfigurációs fájlokat tárolja.

1. Keresse fel az [Azure Portalt](https://portal.azure.com).

1. Nyissa meg [az Azure Cloud Shell](/azure/cloud-shell/overview)t.

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
      # The "feature" block is required for AzureRM provider 2.x. 
      # If you are using version 1.x, the "features" block is not allowed.
      version = "~>2.0"
      features {}
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Mentse a fájlt (**&lt;Ctrl>S),** és lépjen ki a szerkesztőből (**&lt;Ctrl>Q**).

## <a name="define-input-variables"></a>Bemeneti változók meghatározása

Hozza létre azt a Terraform konfigurációs fájlt, amely felsorolja a központi telepítéshez szükséges összes változót.

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

1. Mentse a fájlt (**&lt;Ctrl>S),** és lépjen ki a szerkesztőből (**&lt;Ctrl>Q**).

## <a name="define-the-resources"></a>Az erőforrások meghatározása 
Hozzon létre terraform konfigurációs fájlt, amely létrehozza az összes erőforrást. 

1. Hozzon létre egy `resources.tf` nevű fájlt a Cloud Shellben.

    ```bash
    code resources.tf
    ```

1. Illessze be a következő kódblokkot, hogy helyi blokkot hozzon létre a számított változók számára az újrafelhasználáshoz:

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

1. Illessze be a következő kódblokkot az új felhasználói identitás adatforrásának létrehozásához:

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

1. Illessze be a következő kódblokkot az alaphálózati erőforrások létrehozásához:

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

1. Illessze be a következő kódblokkot az Application Gateway erőforrás létrehozásához:

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

1. A szerepkör-hozzárendelések létrehozásához illessze be a következő kódblokkot:

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

1. Illessze be a következő kódblokkot a Kubernetes-fürt létrehozásához:

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

      default_node_pool {
        name            = "agentpool"
        node_count      = var.aks_agent_count
        vm_size         = var.aks_agent_vm_size
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

1. Mentse a fájlt (**&lt;Ctrl>S),** és lépjen ki a szerkesztőből (**&lt;Ctrl>Q**).

Az ebben a szakaszban bemutatott kód beállítja a fürt nevét, helyét és a resource_group_name. A `dns_prefix` fürt eléréséhez használt teljesen minősített tartománynév (FQDN) részét képező érték van beállítva.

A `linux_profile` rekord lehetővé teszi, hogy konfigurálja azokat a beállításokat, amelyek lehetővé teszik a munkavégző csomópontokba való bejelentkezést az SSH használatával.

Az AKS-sel csak a munkavégző csomópontokért kell fizetnie. A `agent_pool_profile` rekord konfigurálja ezeknek a munkavégző csomópontoknak a részleteit. A `agent_pool_profile record` tartalmazza a létrehozandó munkavégző csomópontok számát és a munkavégző csomópontok típusát. Ha a jövőben felkell skáláznia vagy lefelé kell `count` skáláznia a fürtöt, módosítsa a rekord értékét.

## <a name="create-a-terraform-output-file"></a>Terraform kimeneti fájl létrehozása

[A Terraform kimenetek](https://www.terraform.io/docs/configuration/outputs.html) lehetővé teszik, hogy olyan értékeket határozzon meg, amelyek ki `terraform output` vannak emelve a felhasználó számára, amikor a Terraform tervet alkalmaz, és a parancs segítségével lekérdezhető. Ebben a szakaszban létrehozza a kimeneti fájlt, amellyel hozzáférhet a fürthöz a [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) használatával.

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

1. Mentse a fájlt (**&lt;Ctrl>S),** és lépjen ki a szerkesztőből (**&lt;Ctrl>Q**).

## <a name="configure-azure-storage-to-store-terraform-state"></a>Az Azure storage konfigurálása a Terraform állapotának tárolására

A Terraform helyileg követi nyomon az állapotot a `terraform.tfstate` fájlon keresztül. Ez a minta jól működik egy egyszemélyes környezetben. Azonban egy praktikusabb többszemélyes környezetben, nyomon kell követnie az állapot ot a kiszolgálón az [Azure storage](/azure/storage/)használatával. Ebben a szakaszban megtudhatja, hogy a szükséges tárfiók adatait, és hozzon létre egy tárolótárolót. A Terraform állapotadatok ezután ebben a tárolóban tárolódnak.

1. Az Azure Portalon az **Azure-szolgáltatások**csoportban válassza **a Storage-fiókok**lehetőséget. (Ha a **Tárfiókok** beállítás nem látható a főoldalon, válassza a **További szolgáltatások** lehetőséget, majd keresse meg és jelölje ki.)

1. A **Storage-fiókok** lapon válassza ki annak a tárfióknak a nevét, amelyben a Terraform az állapotot tárolja. Használhatja például azt a tárfiókot is, amely a Cloud Shell első megnyitásakor jött létre.  A Cloud Shell által létrehozott tárfiók neve általában `cs` értékkel kezdődik, amelyet számok és betűk véletlenszerű sorozata követ. 

    Vegye figyelembe a kiválasztott tárfiókot, ahogy később szüksége van rá.

1. A Tárfiók oldalon válassza a **Hozzáférési kulcsok** elemet.

    ![Tárfiók menüje](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Jegyezze fel a **key1** **key** értékét. (A kulcs jobb oldalán található ikonra kattintva a vágólapra másolhatja az értéket.)

    ![Tárfiók hozzáférési kulcsa](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. A Cloud Shellben hozzon létre egy tárolót az Azure storage-fiókjában. Cserélje le a helyőrzőket az Azure storage-fiókjához megfelelő értékekre.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Kubernetes-fürt létrehozása
Ez a szakasz ismerteti, hogyan használható a `terraform init` parancs az előző szakaszokban létrehozott konfigurációs fájlokat meghatározó erőforrások létrehozásához.

1. A Cloud Shell alkalmazásban inicializálja a Terraform ot. Cserélje le a helyőrzőket az Azure storage-fiókjához megfelelő értékekre.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
  
    A `terraform init` parancs a háttér- és szolgáltatóbeépülő modul inicializálásának sikeresét jeleníti meg:

    ![A „terraform init” eredményeit mutató példa](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. A Cloud Shell ben `terraform.tfvars`hozzon létre egy fájl neve:

    ```bash
    code terraform.tfvars
    ```

1. Illessze be a korábban létrehozott alábbi változókat a szerkesztőbe. A környezet helyértékének lekérnie, használja a használatát. `az account list-locations`

    ```hcl
    resource_group_name = "<Name of the Resource Group already created>"

    location = "<Location of the Resource Group>"
      
    aks_service_principal_app_id = "<Service Principal AppId>"
      
    aks_service_principal_client_secret = "<Service Principal Client Secret>"
      
    aks_service_principal_object_id = "<Service Principal Object Id>"
        
    ```

1. Mentse a fájlt (**&lt;Ctrl>S),** és lépjen ki a szerkesztőből (**&lt;Ctrl>Q**).

1. Futtassa a `terraform plan` parancsot az infrastruktúra elemeit meghatározó Terraform-terv létrehozásához. 

    ```bash
    terraform plan -out out.plan
    ```

    A `terraform plan` parancs megjeleníti a `terraform apply` parancs futtatásakor létrehozott erőforrásokat:

    ![A „terraform plan” eredményeit mutató példa](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Futtassa a `terraform apply` parancsot a Kubernetes-fürtöt létrehozó terv alkalmazásához. A Kubernetes-fürt létrehozásának folyamata több percet is igénybe vehet, ami a Cloud Shell munkamenet időtúllépésének csökkenését eredményezi. Ha a Cloud Shell-munkamenet idővel idővel, kövesse a "Helyreállítás a felhőbeli rendszerhéj időmeghosszabbítása" című szakaszban leírt lépéseket, hogy lehetővé tegye az oktatóanyag befejezéséhez.

    ```bash
    terraform apply out.plan
    ```

    A `terraform apply` parancs megjeleníti a konfigurációs fájlokban meghatározott erőforrások létrehozásának eredményét:

    ![A „terraform apply” eredményeit mutató példa](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. Az Azure Portalon válassza a bal oldali menü **erőforráscsoportok** kiválasztásához az új Kubernetes-fürthöz a kiválasztott erőforráscsoportban létrehozott erőforrások megtekintéséhez.

    ![Cloud Shell-parancssor](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Helyreállítás a Cloud Shell időtúllépéséből

Ha a Cloud Shell munkamenet idővel idővel, a következő lépéseket, hogy visszaszerezze:

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

Az Azure Active Directory Pod Identity tokenalapú hozzáférést biztosít az [Azure Resource Manager hez.](/azure/azure-resource-manager/resource-group-overview)

[Az Azure AD Pod Identity](https://github.com/Azure/aad-pod-identity) a következő összetevőket adja hozzá a Kubernetes-fürthöz:

  - Kubernetes [CRD-k:](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/) `AzureIdentity`, `AzureAssignedIdentity``AzureIdentityBinding`
  - [Felügyelt identitásvezérlő (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic) összetevő
  - [Csomópontfelügyelt identitás (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi) összetevője

Ha az RBAC **engedélyezve**van, futtassa a következő parancsot az Azure AD Pod Identity fürtre való telepítéséhez:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
```

Ha az RBAC le van **tiltva,** futtassa a következő parancsot az Azure AD Pod Identity fürtre való telepítéséhez:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
```

## <a name="install-helm"></a>Helm telepítése

Az ebben a szakaszban található kód [a Helm](/azure/aks/kubernetes-helm) - `application-gateway-kubernetes-ingress` Kubernetes csomagkezelőt használja a csomag telepítéséhez:

1. Ha az RBAC **engedélyezve**van, futtassa a Helm telepítéséhez és konfigurálásához szükséges következő parancskészletet:

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

1. Ha az RBAC le van **tiltva,** futtassa a következő parancsot a Helm telepítéséhez és konfigurálásához:

    ```bash
    helm init
    ```

1. Adja hozzá az AGIC Helm adattárat:

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="install-ingress-controller-helm-chart"></a>A be- és nagybe: A vezérlő vezérlőjének diagramja

1. Letöltés `helm-config.yaml` az AGIC konfigurálásához:

    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```

1. Szerkesztsd `helm-config.yaml` a megfelelő `appgw` értékeket és `armAuth` szakaszokat.

    ```bash
    code helm-config.yaml
    ```

    Az értékek leírása a következő:

    - `verbosityLevel`: Beállítja az AGIC naplózási infrastruktúra részletességi szintjét. A lehetséges értékeket a [Naplózási szintek](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) című témakörben olvassa el.
    - `appgw.subscriptionId`: Az App Gateway Azure-előfizetési azonosítója. Például: `a123b234-a3b4-557d-b2df-a0bc12de1234`
    - `appgw.resourceGroup`: Annak az Azure Resource Groupnak a neve, amelyben az App Gateway-t létrehozták. 
    - `appgw.name`: Az alkalmazásátjáró neve. Példa: `applicationgateway1`.
    - `appgw.shared`: Ezt a logikai jelzőt alapértelmezetten a. `false` Ha `true` szüksége van egy [megosztott alkalmazásátjáróra,](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)állítsa be.
    - `kubernetes.watchNamespace`: Adja meg azt a névközt, amelyet az AGIC-nek figyelnie kell. A névtér lehet egyetlen karakterlánc-érték, vagy a névterek vesszővel tagolt listája. Ha ezt a változót figyelmen kívül hagyja, vagy üres vagy üres karakterláncra állítja, a Bejövő vezérlő az összes elérhető névteret figyeli.
    - `armAuth.type`: Az érték `aadPodIdentity` `servicePrincipal`vagy a .
    - `armAuth.identityResourceID`: A felügyelt identitás erőforrásazonosítója.
    - `armAuth.identityClientId`: Az identitás ügyfélazonosítója.
    - `armAuth.secretJSON`: Csak akkor szükséges, ha az `armAuth.type` egyszerű szolgáltatástitkos típust választja (ha a beállítás `servicePrincipal`be van állítva).

    Főbb megjegyzések:
    - Az `identityResourceID` érték a terraform parancsfájlban jön létre, `echo "$(terraform output identity_resource_id)"`és a következőkszerint érhető el: .
    - Az `identityClientID` érték a terraform parancsfájlban jön létre, `echo "$(terraform output identity_client_id)"`és a következőkszerint érhető el: .
    - Az `<resource-group>` érték az App Gateway erőforráscsoportja.
    - Az `<identity-name>` érték a létrehozott identitás neve.
    - Egy adott előfizetés összes identitása a `az identity list`következő használatával jelenik meg: .

1. Az Application Gateway be- és vissza- és visszatelepítése vezérlőcsomagjának telepítése:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

### <a name="install-a-sample-app"></a>Mintaalkalmazás telepítése

Miután telepítette az App Gateway, az AKS és az AGIC alkalmazást, az [Azure Cloud Shell-en](https://shell.azure.com/)keresztül telepíthet egy mintaalkalmazást:

1. A YAML-fájl letöltéséhez használja a curl parancsot:

    ```bash
    curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
    ```

2. A YAML-fájl alkalmazása:

    ```bash
    kubectl apply -f aspnetapp.yaml
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat.  

Cserélje ki a helyőrzőt a megfelelő értékre. A megadott erőforráscsoporton belüli összes erőforrás törlődik.

```azurecli
az group delete -n <resource-group>
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Application Gateway bejövőforgalom-vezérlő](https://azure.github.io/application-gateway-kubernetes-ingress/)