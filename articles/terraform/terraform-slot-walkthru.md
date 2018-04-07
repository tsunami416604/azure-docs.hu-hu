---
title: Az Azure szolgáltató üzembe helyezési pont Terraform
description: Az Azure provider telepítési tárolóhely oktatóanyag Terraform
keywords: terraform, devops, virtuális gép Azure, üzembe helyezési
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 4/05/2018
ms.topic: article
ms.openlocfilehash: 34b16b5fb2b5b574d166693db346ebba15eaa1f9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="using-terraform-to-provision-infrastructure-with-azure-deployment-slots"></a>Kiépítés infrastruktúra és az Azure üzembe helyezési Terraform használata

[Az Azure üzembe helyezési](/azure/app-service/web-sites-staged-publishing) engedélyezi, hogy az alkalmazás - például üzemi és átmeneti - hibás központi telepítésének hatás minimalizálása érdekében különböző verziói között felcserélése. Ez a cikk egy példa az üzembe helyezési érdekében keresztül GitHub és az Azure két alkalmazások központi telepítését mutatja be. Egy alkalmazás tárolja egy "termelési tárolóhelye", míg a második app "előkészítési" pont van tárolva. (A nevek "éles" és "átmeneti" tetszőleges, és azt szeretné, amely jelzi, hogy adott esetben bármilyen.) Az üzembe helyezési pontok konfigurálása után majd segítségével Terraform felcserélése a két üzembe helyezési ponti között, szükség szerint.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) megkezdése előtt.

- **GitHub-fiók** - A [GitHub](http://www.github.com) fiók szükséges elágaztatásáról és a vizsgálat GitHub-tárház használja.

## <a name="create-and-apply-the-terraform-plan"></a>Létrehozása és alkalmazása a Terraform terv

1. Keresse meg a [Azure-portálon](http://portal.azure.com)

1. Nyissa meg [Azure Cloud rendszerhéj](/azure/cloud-shell/overview), és – ha ezt nem tette korábban - **Bash** , a környezetében.

    ![Felhő rendszerhéj kérdés](./media/terraform-slot-walkthru/azure-portal-cloud-shell-button-min.png)

1. Lépjen a `clouddrive` könyvtár.

    ```bash
    cd clouddrive
    ```

1. Hozzon létre egy könyvtárat nevű `deploy`.

    ```bash
    mkdir deploy
    ```

1. Hozzon létre egy könyvtárat nevű `swap`.

    ```bash
    mkdir swap
    ```

1. Győződjön meg arról, hogy mindkét könyvtárak létrehozása sikeresen megtörtént használatával a `ls` bash parancsot.

    ![Könyvtárak létrehozása után felhő rendszerhéj](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. Lépjen a `deploy` könyvtár.

    ```bash
    cd deploy
    ```

1. Használja a [vi szerkesztő](https://www.debian.org/doc/manuals/debian-tutorial/ch-editor.html), hozzon létre egy fájlt `deploy.tf`, fogja tartalmazni, amely a [Terraform konfigurációs](https://www.terraform.io/docs/configuration/index.html).

    ```bash
    vi deploy.tf
    ```

1. Adja meg a beszúrási módban a levél lenyomásával `i` kulcs.

1. Az alábbi kód beillesztése a szerkesztőbe:

    ```JSON
    # Configure the Azure Provider
    provider "azurerm" { }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        app_service_plan_id = "${azurerm_app_service_plan.slotDemo.id}"
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        app_service_plan_id = "${azurerm_app_service_plan.slotDemo.id}"
        app_service_name    = "${azurerm_app_service.slotDemo.name}"
    }
    ```

1. Nyomja meg a  **&lt;Esc >** kulcs az insert módjából való kilépéshez.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs megadásával, majd nyomja le  **&lt;Enter >**:

    ```bash
    :wq
    ```

1. A fájl létrehozása után ellenőrizheti annak tartalmát.

    ```bash
    cat deploy.tf
    ```

1. Terraform inicializálni.

    ```bash
    terraform init
    ```

1. A Terraform terv létrehozásához.

    ```bash
    terraform plan
    ```

1. A megadott erőforrások biztosításához a `deploy.tf` konfigurációs fájlt. (A művelet megerősítéséhez írja be `yes` a parancssorba.)

    ```bash
    terraform apply
    ```

1. Zárja be a felhő rendszerhéj ablakát.

1. Az Azure portál fő menüjében kattintson a **erőforráscsoportok**.

    ![Erőforráscsoportok Azure-portálon](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. Az a **erőforráscsoportok** lapon jelölje be **slotDemoResourceGroup**.

    ![Terraform által létrehozott erőforráscsoport](./media/terraform-slot-walkthru/resource-group.png)

Ha elkészült, lásd: Terraform által létrehozott összes erőforrást.

![Terraform által létrehozott erőforrások](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>Elágazás a test-projekt

Mielőtt a létrehozása és az üzembe helyezési mindkét lecserélés teszteléséhez szüksége oszthatja ketté a teszt projektet a Githubról.

1. Keresse meg a [Soft terraform tárház a Githubon](https://github.com/Azure/awesome-terraform).

1. Elágazás a **Soft terraform tárház**.

    ![Elágazás a GitHub-tárház Soft terraform](./media/terraform-slot-walkthru/fork-repo.png)

1. A környezet oszthatja ketté bármely útmutatást követve.

## <a name="deploy-from-github-to-your-deployment-slots"></a>Az üzembe helyezési pontok telepítése a Githubból

Elágazás a test-projekt tárház egyszer adja meg az üzembe helyezési keresztül az alábbi lépéseket:

1. Az Azure portál fő menüjében kattintson a **erőforráscsoportok**.

1. Válassza ki **slotDemoResourceGroup**.

1. Válassza ki **slotAppService**.

1. Válassza ki **központi telepítési beállítások**.

    ![App Service erőforrás telepítési lehetőségek](./media/terraform-slot-walkthru/deployment-options.png)

1. Az a **rendszerbe állítási beállításának** lapon jelölje be **forrás választása**, majd válassza ki **GitHub**.

    ![Válassza ki a központi telepítés forrása](./media/terraform-slot-walkthru/select-source.png)

1. Miután Azure a kapcsolatot, és megjeleníti a beállításokat, válassza ki **engedélyezési**.

1. Az a **engedélyezési** lapon jelölje be **engedélyezés**, és az Azure a GitHub-fiók eléréséhez szükséges hitelesítő adatokat. 

1. Miután az Azure ellenőrzi a Githubon hitelesítő adatait, megjelenik egy üzenet azt jelzi, hogy az engedélyezési folyamat befejeződött. Válassza ki **OK** bezárásához a **engedélyezési** fülre.

1. Válassza ki **válassza ki a szervezet** , és jelölje ki a szervezetét.

1. Válassza ki **válasszon projekt**.

1. Az a **válasszon projekt** lapon jelölje be a **Soft terraform** projekt.

    ![Válassza ki a Soft terraform projekt](./media/terraform-slot-walkthru/choose-project.png)

1. Válassza ki **válasszon fiókirodai**.

1. Az a **válasszon fiókirodai** lapon jelölje be **fő**.

    ![Válassza ki a főágba](./media/terraform-slot-walkthru/choose-branch-master.png)

1. Az a **rendszerbe állítási beállításának** lapon jelölje be **OK**.

Ezen a ponton telepítette az éles webalkalmazásra. Az előkészítési pont telepítéséhez hajtsa végre az előző lépéseket ebben a szakaszban a következő módosításokkal:

- A 3 **slotAppServiceSlotOne** erőforrás.

- 13. lépést válassza ki a "" munkaágat a főágba helyett.

    ![Válassza ki a fiókiroda használata](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>Az alkalmazások telepítésének tesztelése

A korábbi szakaszokban, állítsa be két üzembe helyezési ponti - **slotAppService** és **slotAppServiceSlotOne** - ággal a Githubon történő központi telepítéséhez. Most tekintse meg a webes alkalmazások ellenőrzése, hogy azok sikeresen telepített.

A következő lépésekkel kétszer ahol a 3. lépésben ki kell választania **slotAppService** először, és válassza **slotAppServiceSlotOne** a második alkalommal:

1. Az Azure portál fő menüjében kattintson a **erőforráscsoportok**.

1. Válassza ki **slotDemoResourceGroup**.

1. Válassza ki vagy **slotAppService** vagy **slotAppServiceSlotOne**.

1. A – Áttekintés lapon válassza az **URL-cím**.

    ![Válassza ki az URL-cím – Áttekintés lapon lehet megjeleníteni az alkalmazás](./media/terraform-slot-walkthru/resource-url.png)

> [!NOTE]
> Azure-beli létrehozásához és telepítéséhez a Githubból a hely több percet is igénybe vehet.
>
>

Az a **slotAppService** web app alkalmazásban egy kék oldal rendelkező az oldal akkor jelenik meg **tárolóhely bemutató alkalmazás 1**. Az a **slotAppServiceSlotOne** webalkalmazás, megjelenik egy zöld lapon a lap címe **tárolóhely bemutató alkalmazás 2**.

![Tekintse meg az alkalmazások teszteléséhez, hogy azok lettek a megfelelő telepítést](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>A két üzembe helyezési felcserélése

Csere a két üzembe helyezési tárhely, hajtsa végre az alábbi lépéseket:
 
1. Váltás a futó böngészőlapon **slotAppService** (az alkalmazás és a kék lap). 

1. Térjen vissza az Azure portálra az egy-egy külön lapján.

1. Nyissa meg a felhő rendszerhéjat.

1. Lépjen a **clouddrive/csere** könyvtár.

    ```bash
    cd clouddrive/swap
    ```

1. A vi-szerkesztő segítségével hozzon létre egy fájlt `swap.tf`.

    ```bash
    vi swap.tf
    ```

1. Adja meg a beszúrási módban a levél lenyomásával `i` kulcs.

1. Az alábbi kód beillesztése a szerkesztőbe:

    ```JSON
    # Configure the Azure Provider
    provider "azurerm" { }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Nyomja meg a  **&lt;Esc >** kulcs az insert módjából való kilépéshez.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs megadásával, majd nyomja le  **&lt;Enter >**:

    ```bash
    :wq
    ```

1. Terraform inicializálni.

    ```bash
    terraform init
    ```

1. A Terraform terv létrehozásához.

    ```bash
    terraform plan
    ```

1. A megadott erőforrások biztosításához a `swap.tf` konfigurációs fájlt. (A művelet megerősítéséhez írja be `yes` a parancssorba.)

    ```bash
    terraform apply
    ```

1. Miután befejezte a Terraform áttelepíteni a forráskörnyezetból a tárhelyek, a böngészőben lépjen vissza, amely megjeleníti a **slotAppService** webalkalmazás, és frissítse az oldalt. 

A webalkalmazás a **slotAppServiceSlotOne** tárolóhely átmeneti rendelkezik az éles tárolóhelyre lett cserél, és most Renderelés zöld. 

![Az üzembe helyezési rendelkezik lett cserélve.](./media/terraform-slot-walkthru/slots-swapped.png)

Az alkalmazás eredeti éles verzióját visszaállításához alkalmazza újra a Terraform terv alapján létrehozott a `swap.tf` konfigurációs fájlt.

```bash
terraform apply
```

Miután cserélni, lásd: az eredeti konfigurációt.