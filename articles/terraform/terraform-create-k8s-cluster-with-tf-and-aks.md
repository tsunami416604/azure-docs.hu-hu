---
title: Oktatóanyag – Kubernetes-fürt létrehozása az Azure Kubernetes szolgáltatással (AKS) a Terraform használatával
description: Ebben az oktatóanyagban kubernetes-fürtöt hoz létre az Azure Kubernetes-szolgáltatással és a Terraform-mal
keywords: azure devops terraform aks kubernetes
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 0a193c8da6441a04f742894797521fe92f26b2e1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945298"
---
# <a name="tutorial-create-a-kubernetes-cluster-with-azure-kubernetes-service-using-terraform"></a>Oktatóanyag: Hozzon létre egy Kubernetes-fürtöt az Azure Kubernetes szolgáltatással a Terraform használatával

[Az Azure Kubernetes-szolgáltatás (AKS)](/azure/aks/) kezeli a üzemeltetett Kubernetes-környezetet. Az AKS lehetővé teszi a tárolóba rendezett alkalmazások üzembe helyezését és kezelését a tárolóvezénylési szakértelem nélkül. Az AKS számos gyakori karbantartási műveletet is lehetővé tesz anélkül, hogy az alkalmazást offline állapotba hozná. Ezek a műveletek közé tartozik a kiépítés, a frissítés és az erőforrások igény szerinti méretezése.

Ebben az oktatóanyagban megtudhatja, hogyan kell elvégezni a következő feladatokat:

> [!div class="checklist"]
> * Kubernetes-fürt meghatározása HCL (HashiCorp Language) használatával
> * Kubernetes-fürt létrehozása Terraformmal és AKS-sel
> * Kubernetes-fürt rendelkezésre állásának tesztelése a kubectl eszközzel

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **A Terraform konfigurálása**: Kövesse a [Terraform telepítését és az Azure-hozzáférés konfigurálását ismertető cikkben](terraform-install-configure.md) található utasításokat

- **Azure-beli szolgáltatásnév**: Kövesse az [Azure-beli szolgáltatásnév létrehozása az Azure CLI-vel](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) című cikk **a szolgáltatásnév létrehozását** ismertető szakaszában foglaltakat. Jegyezze fel az appId, a displayName, a password és a tenant értékét.

## <a name="create-the-directory-structure"></a>A könyvtárstruktúra létrehozása

Az első lépés a könyvtár létrehozása, amely a feladathoz tartozó Terraform konfigurációs fájlokat tárolja.

1. Keresse fel az [Azure Portalt](https://portal.azure.com).

1. Nyissa meg [az Azure Cloud Shell](/azure/cloud-shell/overview)t. Ha még nem választott ki környezetet, válassza a **Bash** környezetet.

    ![Cloud Shell-parancssor](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Lépjen be a `clouddrive` könyvtárba.

    ```bash
    cd clouddrive
    ```

1. Hozzon létre egy `terraform-aks-k8s` nevű könyvtárat.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Lépjen be az új könyvtárba:

    ```bash
    cd terraform-aks-k8s
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

## <a name="define-a-kubernetes-cluster"></a>Kubernetes-fürt meghatározása

Hozza létre a Kubernetes-fürt erőforrásait deklaráló Terraform konfigurációs fájlt.

1. Hozzon létre egy `k8s.tf` nevű fájlt a Cloud Shellben.

    ```bash
    code k8s.tf
    ```

1. Másolja az alábbi kódot a szerkesztőbe:

    ```hcl
    resource "azurerm_resource_group" "k8s" {
        name     = var.resource_group_name
        location = var.location
    }
    
    resource "random_id" "log_analytics_workspace_name_suffix" {
        byte_length = 8
    }

    resource "azurerm_log_analytics_workspace" "test" {
        # The WorkSpace name has to be unique across the whole of azure, not just the current subscription/tenant.
        name                = "${var.log_analytics_workspace_name}-${random_id.log_analytics_workspace_name_suffix.dec}"
        location            = var.log_analytics_workspace_location
        resource_group_name = azurerm_resource_group.k8s.name
        sku                 = var.log_analytics_workspace_sku
    }

    resource "azurerm_log_analytics_solution" "test" {
        solution_name         = "ContainerInsights"
        location              = azurerm_log_analytics_workspace.test.location
        resource_group_name   = azurerm_resource_group.k8s.name
        workspace_resource_id = azurerm_log_analytics_workspace.test.id
        workspace_name        = azurerm_log_analytics_workspace.test.name

        plan {
            publisher = "Microsoft"
            product   = "OMSGallery/ContainerInsights"
        }
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = var.cluster_name
        location            = azurerm_resource_group.k8s.location
        resource_group_name = azurerm_resource_group.k8s.name
        dns_prefix          = var.dns_prefix

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
                key_data = file(var.ssh_public_key)
            }
        }

        default_node_pool {
            name            = "agentpool"
            node_count      = var.agent_count
            vm_size         = "Standard_DS1_v2"
        }

        service_principal {
            client_id     = var.client_id
            client_secret = var.client_secret
        }

        addon_profile {
            oms_agent {
            enabled                    = true
            log_analytics_workspace_id = azurerm_log_analytics_workspace.test.id
            }
        }

        tags = {
            Environment = "Development"
        }
    }
    ```

    Az előző kód beállítja a fürt nevét, helyét és az erőforráscsoport nevét. A teljesen minősített tartománynév (FQDN) előtagja is be van állítva. A teljes tartománynhálózat a fürt elérésére szolgál.

    A `linux_profile` rekord lehetővé teszi, hogy konfigurálja azokat a beállításokat, amelyek lehetővé teszik a munkavégző csomópontokba való bejelentkezést az SSH használatával.

    Az AKS-sel csak a munkavégző csomópontokért kell fizetnie. A `default_node_pool` rekord konfigurálja ezeknek a munkavégző csomópontoknak a részleteit. A `default_node_pool record` tartalmazza a létrehozandó munkavégző csomópontok számát és a munkavégző csomópontok típusát. Ha a jövőben felkell skáláznia vagy lefelé kell `count` skáláznia a fürtöt, módosítsa a rekord értékét.

1. Mentse a fájlt (**&lt;Ctrl>S),** és lépjen ki a szerkesztőből (**&lt;Ctrl>Q**).

## <a name="declare-the-variables"></a>Változók deklarálása

1. Hozzon létre egy `variables.tf` nevű fájlt a Cloud Shellben.

    ```bash
    code variables.tf
    ```

1. Másolja az alábbi kódot a szerkesztőbe:

    ```hcl
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }

    variable log_analytics_workspace_name {
        default = "testLogAnalyticsWorkspaceName"
    }

    # refer https://azure.microsoft.com/global-infrastructure/services/?products=monitor for log analytics available regions
    variable log_analytics_workspace_location {
        default = "eastus"
    }

   # refer https://azure.microsoft.com/pricing/details/monitor/ for log analytics pricing 
   variable log_analytics_workspace_sku {
        default = "PerGB2018"
   }
    ```

1. Mentse a fájlt (**&lt;Ctrl>S),** és lépjen ki a szerkesztőből (**&lt;Ctrl>Q**).

## <a name="create-a-terraform-output-file"></a>Terraform kimeneti fájl létrehozása

A [Terraform-kimenetek](https://www.terraform.io/docs/configuration/outputs.html) lehetővé teszik, hogy megadja azokat az értékeket, amelyek ki lesznek emelve a felhasználó számára a Terraform-tervek alkalmazásakor, és lekérdezhetők a `terraform output` paranccsal. Ebben a szakaszban létrehozza a kimeneti fájlt, amellyel hozzáférhet a fürthöz a [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) használatával.

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
    ```

1. Mentse a fájlt (**&lt;Ctrl>S),** és lépjen ki a szerkesztőből (**&lt;Ctrl>Q**).

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Az Azure Storage beállítása Terraform-állapot tárolásához

A Terraform helyileg követi nyomon az állapotot a `terraform.tfstate` fájlon keresztül. Ez a minta jól működik egy egyszemélyes környezetben. Többszemélyes környezetben az [Azure storage](/azure/storage/) az állapot nyomon követésére szolgál.

Ebben a szakaszban a következő feladatok at láthatja:
- Tárfiók adatainak lekérése (fióknév és fiókkulcs)
- Hozzon létre egy tárolótárolót, amelyben a Terraform állapotadatok at tárolják.

1. Az Azure Portalon a bal oldali menüben válassza a **Minden szolgáltatás** elemet.

1. Válassza a **Tárfiókok** lehetőséget.

1. A **Tárfiókok** lapon válassza ki annak a tárfióknak nevét, amelyben a Terraform fogja tárolni az állapotot. Használhatja például azt a tárfiókot is, amely a Cloud Shell első megnyitásakor jött létre.  A Cloud Shell által létrehozott tárfiók neve általában `cs` értékkel kezdődik, amelyet számok és betűk véletlenszerű sorozata követ. Vegye figyelembe a kiválasztott tárfiókot. Erre az értékre később van szükség.

1. A tárfiók lapon válassza a **Hozzáférési kulcsok** lehetőséget.

    ![Tárfiók menüje](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Jegyezze fel a **key1** **key** értékét. (A kulcs jobb oldalán található ikonra kattintva a vágólapra másolhatja az értéket.)

    ![Tárfiók hozzáférési kulcsa](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. A Cloud Shellben hozzon létre egy tárolót az Azure storage-fiókjában. Cserélje le a helyőrzőket a környezetének megfelelő értékekre.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Kubernetes-fürt létrehozása

Ez a szakasz ismerteti, hogyan használható a `terraform init` parancs az előző szakaszokban létrehozott konfigurációs fájlokat meghatározó erőforrások létrehozásához.

1. A Cloud Shell alkalmazásban inicializálja a Terraform ot. Cserélje le a helyőrzőket a környezetének megfelelő értékekre.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    A `terraform init` parancs a háttér- és szolgáltatóbeépülő modul inicializálásának sikeresét jeleníti meg:

    ![A „terraform init” eredményeit mutató példa](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Exportálja az egyszerű szolgáltatásnév hitelesítő adatait. Cserélje le a helyőrzőket a szolgáltatásnév megfelelő értékeire.

    ```bash
    export TF_VAR_client_id=<service-principal-appid>
    export TF_VAR_client_secret=<service-principal-password>
    ```

1. Futtassa a `terraform plan` parancsot az infrastruktúra elemeit meghatározó Terraform-terv létrehozásához. 

    ```bash
    terraform plan -out out.plan
    ```

    A `terraform plan` parancs megjeleníti azokat az erőforrásokat, amelyek a `terraform apply` parancs futtatásakor jönnek létre:

    ![A „terraform plan” eredményeit mutató példa](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Futtassa a `terraform apply` parancsot a Kubernetes-fürtöt létrehozó terv alkalmazásához. A Kubernetes-fürt létrehozásának folyamata több percet is igénybe vehet, ami a Cloud Shell munkamenet időtúllépésének csökkenését eredményezi. Ha a Cloud Shell-munkamenet idővel idővel, kövesse a "Helyreállítás a felhőbeli rendszerhéj időmeghosszabbítása" című szakaszban leírt lépéseket, hogy lehetővé tegye az oktatóanyag befejezéséhez.

    ```bash
    terraform apply out.plan
    ```

    A `terraform apply` parancs megjeleníti a konfigurációs fájlokban meghatározott erőforrások létrehozásának eredményét:

    ![A „terraform apply” eredményeit mutató példa](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. Az Azure Portalon válassza a bal oldali menü **minden erőforrást** az új Kubernetes-fürthöz létrehozott erőforrások megtekintéséhez.

    ![Az Azure Portal összes erőforrása](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Helyreállítás a Cloud Shell időtúllépéséből

Ha a Cloud Shell munkamenet idővel ki, a következő lépéseket a helyreállításhoz:

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

    ![A kubectl eszközzel ellenőrizheti a Kubernetes-fürt állapotát](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="monitor-health-and-logs"></a>Állapot és naplók monitorozása

Az AKS-fürt létrejöttekor a monitorozás is engedélyezve lett, hogy rögzítse a fürtcsomópontok és a podok állapotmetrikáit. Ezek az állapotmetrikák elérhetők az Azure Portalon. A tároló állapotának monitorozásával kapcsolatos további információ [az Azure Kubernetes Service állapotmonitorozásáról](/azure/azure-monitor/insights/container-insights-overview) szóló témakörben érhető el.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [További információ a Terraform azure-beli használatáról](/azure/terraform)