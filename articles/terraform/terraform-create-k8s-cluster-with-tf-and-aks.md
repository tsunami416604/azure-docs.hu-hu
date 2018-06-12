---
title: Az Azure Kubernetes szolgáltatás (AKS) és Terraform Kubernetes fürt létrehozása
description: Az oktatóanyag egy Kubernetes fürt létrehozása az Azure Kubernetes szolgáltatás és Terraform ábrázoló
keywords: terraform, a devops, a virtuális gépet, az azure, a kubernetes
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 06/11/2018
ms.topic: article
ms.openlocfilehash: bd00a0cc8446802a03570edd58949a46c0769101
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303750"
---
# <a name="create-a-kubernetes-cluster-with-azure-kubernetes-service-and-terraform"></a>Az Azure Kubernetes szolgáltatás és Terraform Kubernetes fürt létrehozása
[Az Azure Kubernetes szolgáltatás (AKS)](/azure/aks/) kezeli az üzemeltetett Kubernetes-környezethez, így gyorsan és egyszerűen telepítéséhez és kezeléséhez indexelése alkalmazások tároló vezénylési szakértői nélkül. Ezenkívül a folyamatban lévő műveletek és karbantartás terhét is megszünteti az erőforrások igény szerinti kiépítésével, frissítésével és méretezésével anélkül, hogy offline állapotba kellene helyezni az alkalmazásait.

Ebben az oktatóanyagban elsajátíthatja, hogyan létrehozásakor a következő feladatok végezhetők el a [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) fürt használatával [Terraform](http://terraform.io) és AKS:

> [!div class="checklist"]
> * Hardverkompatibilitási (HashiCorp Language) segítségével Kubernetes fürt meghatározása
> * Használja a Terraform és AKS Kubernetes fürt létrehozása
> * A kubectl eszközzel ellenőrizze az Kubernetes fürt

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Terraform konfigurálása**: a cikk utasításait követve [Terraform és Azure való hozzáférés konfigurálása](/azure/virtual-machines/linux/terraform-install-configure)

- **Az Azure szolgáltatás egyszerű**: szakaszának útmutatásait a **a szolgáltatásnevet létrehozni** című cikk, [hozzon létre egy Azure szolgáltatás egyszerű Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal). Jegyezze fel az appId, displayName, jelszót és bérlői értékeit.

## <a name="create-the-directory-structure"></a>A directory-struktúra létrehozása
Az első lépés, amely tárolja a Terraform konfigurációs fájljait a gyakorlatban a következő könyvtár létrehozásakor.

1. Keresse meg a [Azure-portálon](http://portal.azure.com).

1. Nyissa meg [Azure felhőben rendszerhéj](/azure/cloud-shell/overview). Ha korábban nem jelölt ki egy olyan környezetben, válassza ki a **Bash** , a környezetben.

    ![Felhő rendszerhéj kérdés](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Lépjen a `clouddrive` könyvtár.

    ```bash
    cd clouddrive
    ```

1. Hozzon létre egy könyvtárat nevű `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Váltson át az új könyvtár:

    ```bash
    cd terraform-aks-k8s
    ```

## <a name="declare-the-azure-provider"></a>Deklarálja az Azure-szolgáltató
Az Azure-szolgáltató a Terraform konfigurációs fájl létrehozása.

1. A felhő rendszerhéj, hozzon létre egy fájlt `main.tf`.

    ```bash
    vi main.tf
    ```

1. Adja meg a beszúrási módban kiválasztásával a kulcsot.

1. Az alábbi kód beillesztése a szerkesztőbe:

    ```JSON
    provider "azurerm" {
        version = "=1.5.0"
    }

    terraform {
        backend "azurerm" {}
    }

    ```

1. Kilépés beszúrási módban kiválasztásával a **Esc** kulcs.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

    ```bash
    :wq
    ```

## <a name="define-a-kubernetes-cluster"></a>Adja meg a Kubernetes fürt
Deklarálja az erőforrásokat a Kubernetes fürt Terraform konfigurációs fájl létrehozása.

1. A felhő rendszerhéj, hozzon létre egy fájlt `k8s.tf`.

    ```bash
    vi k8s.tf
    ```

1. Adja meg a beszúrási módban kiválasztásával a kulcsot.

1. Az alábbi kód beillesztése a szerkesztőbe:

    ```JSON
    resource "azurerm_resource_group" "k8s" {
        name     = "${var.resource_group_name}"
        location = "${var.location}"
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = "${var.cluster_name}"
        location            = "${azurerm_resource_group.k8s.location}"
        resource_group_name = "${azurerm_resource_group.k8s.name}"
        dns_prefix          = "${var.dns_prefix}"

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
            key_data = "${file("${var.ssh_public_key}")}"
            }
        }

        agent_pool_profile {
            name            = "default"
            count           = "${var.agent_count}"
            vm_size         = "Standard_D2"
            os_type         = "Linux"
            os_disk_size_gb = 30
        }

        service_principal {
            client_id     = "${var.client_id}"
            client_secret = "${var.client_secret}"
        }

        tags {
            Environment = "Development"
        }
    }
    ```

    Az előzőekben látható kód állítja be a fürtöt, helyét és a resource_group_name nevét. Emellett a dns_prefix - a teljesen minősített tartománynevét (FQDN), amellyel elérhető a fürt részét képező - értéke.

    A **linux_profile** rekord lehetővé teszi a beállítások, amelyek lehetővé teszik a feldolgozó csomópontok SSH használatával bejelentkezni.

    A AKS csak fizetnie a munkavégző csomópontokhoz. A **agent_pool_profile** rekord konfigurálja a feldolgozó csomópontok részleteit. A **agent_pool_profile rekord** létrehozásához a feldolgozó csomópontok száma és a feldolgozó csomópontok típusú tartalmaz. Növelheti vagy csökkentheti a jövőben a fürthöz kell, ha módosítja a **száma** értékét az rekordban.

1. Kilépés beszúrási módban kiválasztásával a **Esc** kulcs.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

    ```bash
    :wq
    ```

## <a name="declare-the-variables"></a>Deklarálja a változókat

1. A felhő rendszerhéj, hozzon létre egy fájlt `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Adja meg a beszúrási módban kiválasztásával a kulcsot.

1. Az alábbi kód beillesztése a szerkesztőbe:

    ```JSON
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
    ```

1. Kilépés beszúrási módban kiválasztásával a **Esc** kulcs.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

    ```bash
    :wq
    ```

## <a name="create-a-terraform-output-file"></a>Egy Terraform a kimeneti fájl létrehozása
[Terraform kimenete](https://www.terraform.io/docs/configuration/outputs.html) határozhatja meg a értékeket, amelyeket a program kiemeli a felhasználó Terraform terv vonatkozik, és segítségével lehet lekérdezni a `terraform output` parancsot. Ebben a szakaszban egy kimeneti fájl, amely lehetővé teszi a hozzáférést a fürt létrehozása [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

1. A felhő rendszerhéj, hozzon létre egy fájlt `output.tf`.

    ```bash
    vi output.tf
    ```

1. Adja meg a beszúrási módban kiválasztásával a kulcsot.

1. Az alábbi kód beillesztése a szerkesztőbe:

    ```JSON
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

1. Kilépés beszúrási módban kiválasztásával a **Esc** kulcs.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

    ```bash
    :wq
    ```

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Állítsa be az Azure storage Terraform állapot tárolásához
Keresztül helyileg állapot Terraform nyomon követi a `terraform.tfstate` fájlt. Ebben a mintában remekül működik egy egyszeri-személy környezetben. Azonban több gyakorlati több résztvevős környezetben kell nyomon követnie állapota a kiszolgáló használ a [az Azure storage](/azure/storage/). Ebben a szakaszban beolvasni a szükséges tárfiók adatait (a fióknevet és a fiókkulcsot), és hozzon létre egy tárolót, amelyben a Terraform állapot adatait tárolja majd.

1. Válassza ki az Azure-portálon **minden szolgáltatás** a bal oldali menüben.

1. Válassza ki **tárfiókok**.

1. Az a **tárfiókok** lapra, válassza ki a tárfiók, amelybe Terraform állapot tárolására neve. Például a tárolási fiók jön létre, amikor első alkalommal megnyitott felhő rendszerhéj is használhatja.  A tárfiók nevéhez létrehozott felhőalapú rendszerhéj általában kezdődik `cs` számok és betűk véletlenszerű karakterlánc követ. **Ne feledje választja, a tárfiók nevét, később igény szerint.**

1. Válassza ki a tárolási fiók lapon **hívóbetűk**.

    ![Tárolási felhasználóifiók-menüjéből](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Jegyezze fel a **key1** **kulcs** érték. (A kulcs jobbra látható ikonra az érték a vágólapra másolja.)

    ![Fiók tárelérési kulcsok](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. Felhő rendszerhéj, hozzon létre egy tároló az Azure-tárfiókja (cserélje le a &lt;YourAzureStorageAccountName > és &lt;YourAzureStorageAccountAccessKey > helyőrzőt a megfelelő értékeket az Azure storage-fiók ).

    ```bash
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>A Kubernetes fürt létrehozása
Ebben a szakaszban látható használata a `terraform init` definiált a konfigurációs fájlokat, a korábbi szakaszokban létrehozott parancs az erőforrások létrehozásához.

1. A felhő rendszerhéj inicializálása Terraform (cserélje le a &lt;YourAzureStorageAccountName > és &lt;YourAzureStorageAccountAccessKey > helyőrzőt a megfelelő értékeket az Azure storage-fiókok).

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    A `terraform init` parancs megjeleníti a háttér és a szolgáltató beépülő modul inicializálása sikeres:

    !["Terraform init" eredmények – példa](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Futtassa a `terraform plan` parancsot a Terraform terv, amely meghatározza az infrastruktúra elemeinek létrehozásához. A parancs fog igényelni a két érték: **var.client_id** és **var.client_secret**. Az a **var.client_id** változó, adja meg a **appId** rendelt érték, amely a szolgáltatás egyszerű. Az a **var.client_secret** változó, adja meg a **jelszó** rendelt érték, amely a szolgáltatás egyszerű.

    ```bash
    terraform plan -out out.plan
    ```

    A `terraform plan` parancs megjeleníti az erőforrásokat, amelyek fog létrejönni, ha futtatja a `terraform apply` parancs:

    !["Terraform terv" eredmények – példa](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Futtassa a `terraform apply` parancsot a terv a Kubernetes fürt létrehozásához. A folyamat Kubernetes fürt létrehozása eltarthat néhány percig, ami azt eredményezi, hogy a felhő rendszerhéj munkamenet időtúllépés. Ha a felhő rendszerhéj munkamenet időkorlátja lejár, az a szakaszban található lépéseket követheti ["Felhő rendszerhéj időtúllépés helyreállíthatók"](#recover-from-a-dloud-shell-timeout) ahhoz, hogy az oktatóanyag elvégzéséhez.

    ```bash
    terraform apply out.plan
    ```

    A `terraform apply` parancsot a konfigurációs fájlokban definiálva az erőforrások létrehozásának eredményeit jeleníti meg:

    ![Az eredmények "terraform alkalmazni" – példa](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. Válassza ki az Azure-portálon **minden szolgáltatás** a bal oldali menü az új Kubernetese fürthöz létrehozott cikkekben találhat.

    ![Felhő rendszerhéj kérdés](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Felhő rendszerhéj időtúllépés helyreállítása
Ha a felhő rendszerhéj munkamenet időkorlátja lejár, a következő lépésekkel helyreállíthat végezheti el:

1. Felhő rendszerhéj munkamenet indításához.

1. Módosítsa a Terraform konfigurációs fájlokat tartalmazó könyvtárat.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Futtassa az alábbi parancsot:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>A Kubernetes fürt tesztelése
A Kubernetes eszközök segítségével ellenőrizze az újonnan létrehozott fürt.

1. A Kubernetes konfigurációs beszerezni Terraform állapotát és azt egy fájlban tárolhatja, hogy kubectl olvashatja.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Így kubectl szerzi be a megfelelő konfigurációs környezeti változó értéke.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. A fürt állapotának ellenőrzése.

    ```bash
    kubectl get nodes
    ```

    A feldolgozó csomópontok részletével kell megjelennie, és a összes állapot **készen**, a következő ábrán látható módon:

    ![A kubectl eszköz lehetővé teszi a Kubernetes fürt állapotának ellenőrzése](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, Terraform és AKS használata Kubernetes fürt létrehozásához. Az alábbiakban néhány további források további tudnivalók az Azure-on Terraform: 

 [A Microsoft.com Terraform Hub](https://docs.microsoft.com/azure/terraform/)  
 [Terraform Azure-szolgáltató dokumentáció](http://aka.ms/terraform)  
 [Terraform Azure szolgáltató forrás](http://aka.ms/tfgit)  
 [Terraform Azure modulok](http://aka.ms/tfmodules)