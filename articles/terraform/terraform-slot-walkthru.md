---
title: Az Azure szolgáltató üzembe helyezési Terraform
description: Terraform használata Azure szolgáltató üzembe helyezési oktatóanyag
keywords: terraform, devops, virtuális gép Azure, üzembe helyezési
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 4/05/2018
ms.topic: article
ms.openlocfilehash: 3a018dbaf90801604b13efcf8bd7afb6dbc68659
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-terraform-to-provision-infrastructure-with-azure-deployment-slots"></a>Kiépítés infrastruktúra és az Azure üzembe helyezési Terraform segítségével

Használhat [Azure üzembe helyezési](/azure/app-service/web-sites-staged-publishing) felcserélni az alkalmazás különböző verziói között. Hibás központi telepítésének hatás minimalizálása érdekében, hogy képes segítségével. 

Ez a cikk egy példa az üzembe helyezési érdekében keresztül GitHub és az Azure két alkalmazások központi telepítését mutatja be. Egy alkalmazás az éles tárhely tárolja. A második alkalmazást az előkészítési pont tárolja. (A nevek "éles" és "átmeneti" tetszőleges, és azt szeretné, amely jelzi, hogy adott esetben bármilyen.) Miután konfigurálta az üzembe helyezési, Terraform segítségével igény szerint a két tárhelyek felcserélése.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **GitHub-fiók**: van szüksége egy [GitHub](http://www.github.com) elágaztatásáról és GitHub-tárház próba fiókkal.

## <a name="create-and-apply-the-terraform-plan"></a>Létrehozása és alkalmazása a Terraform terv

1. Keresse meg a [Azure-portálon](http://portal.azure.com).

1. Nyissa meg [Azure felhőben rendszerhéj](/azure/cloud-shell/overview). Ha korábban nem jelölt ki egy olyan környezetben, válassza ki a **Bash** , a környezetben.

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

1. Használja a `ls` parancs futtatásával ellenőrizze, hogy sikeresen létrehozta a két címtárszolgáltatás bash.

    ![Könyvtárak létrehozása után felhő rendszerhéj](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. Lépjen a `deploy` könyvtár.

    ```bash
    cd deploy
    ```

1. Használatával a [vi szerkesztő](https://www.debian.org/doc/manuals/debian-tutorial/ch-editor.html), hozzon létre egy fájlt `deploy.tf`. Ez a fájl tartalmazza a [Terraform konfigurációs](https://www.terraform.io/docs/configuration/index.html).

    ```bash
    vi deploy.tf
    ```

1. Adja meg a beszúrási módban kiválasztásával a kulcsot.

1. Az alábbi kód beillesztése a szerkesztőbe:

    ```JSON
    # Configure the Azure provider
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

1. Válassza ki az Esc billentyűt az insert módjából való kilépéshez.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

    ```bash
    :wq
    ```

1. Most, hogy a fájl létrehozott, ellenőrizze annak tartalmát.

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

1. A főmenü az Azure portál, válassza ki a **erőforráscsoportok**.

    !["Erőforráscsoportok" kiválasztása a portálon](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. Az a **erőforráscsoportok** lapon jelölje be **slotDemoResourceGroup**.

    ![Terraform által létrehozott erőforráscsoport](./media/terraform-slot-walkthru/resource-group.png)

Ekkor megjelenik az erőforrások Terraform létrehozva.

![Terraform által létrehozott erőforrások](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>Elágazás a test-projekt

Mielőtt a létrehozása és az üzembe helyezési mindkét lecserélés teszteléséhez szüksége oszthatja ketté a teszt projektet a Githubról.

1. Keresse meg a [Soft terraform tárház a Githubon](https://github.com/Azure/awesome-terraform).

1. Elágazás a **Soft terraform** tárházban.

    ![Elágazás a GitHub-tárház Soft terraform](./media/terraform-slot-walkthru/fork-repo.png)

1. A környezet oszthatja ketté bármely útmutatást követve.

## <a name="deploy-from-github-to-your-deployment-slots"></a>Az üzembe helyezési pontok telepítése a Githubból

Követően a teszt projekt tárház oszthatja ketté, adja meg az üzembe helyezési keresztül az alábbi lépéseket:

1. A főmenü az Azure portál, válassza ki a **erőforráscsoportok**.

1. Válassza ki **slotDemoResourceGroup**.

1. Válassza ki **slotAppService**.

1. Válassza ki **központi telepítési beállítások**.

    ![App Service erőforrás telepítési lehetőségek](./media/terraform-slot-walkthru/deployment-options.png)

1. Az a **rendszerbe állítási beállításának** lapon jelölje be **forrás választása**, majd válassza ki **GitHub**.

    ![Válassza ki a központi telepítés forrása](./media/terraform-slot-walkthru/select-source.png)

1. Miután Azure a kapcsolatot, és megjeleníti a beállításokat, válassza ki **engedélyezési**.

1. Az a **engedélyezési** lapon jelölje be **engedélyezés**, és az Azure a GitHub-fiók hozzáférésre van szüksége a hitelesítő adatokat. 

1. Miután Azure ellenőrzi a Githubon hitelesítő adatait, egy üzenet jelenik meg, és azt jelzi, hogy az engedélyezési folyamat befejeződött. Válassza ki **OK** bezárásához a **engedélyezési** fülre.

1. Válassza ki **válassza ki a szervezet** , és jelölje ki a szervezetét.

1. Válassza ki **válasszon projekt**.

1. Az a **válasszon projekt** lapon jelölje be a **Soft terraform** projekt.

    ![Válassza ki a Soft terraform projekt](./media/terraform-slot-walkthru/choose-project.png)

1. Válassza ki **válasszon fiókirodai**.

1. Az a **válasszon fiókirodai** lapon jelölje be **fő**.

    ![Válassza ki a főágba](./media/terraform-slot-walkthru/choose-branch-master.png)

1. Az a **rendszerbe állítási beállításának** lapon jelölje be **OK**.

Ezen a ponton telepítette az éles webalkalmazásra. Az előkészítési pont telepítéséhez hajtsa végre az előző lépéseket ebben a szakaszban a következő módosításokkal:

- A 3. lépésben válassza ki a **slotAppServiceSlotOne** erőforrás.

- 13. lépést válassza ki a munkaágat a főágba helyett.

    ![Válassza ki a munkaágat](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>Az alkalmazások telepítésének tesztelése

A korábbi szakaszokban, állítsa be két üzembe helyezési ponti--**slotAppService** és **slotAppServiceSlotOne**--ággal a Githubon történő központi telepítéséhez. Most tekintse meg a webes alkalmazások ellenőrzése, hogy azok sikeresen telepített.

A következő lépésekkel kétszer. A 3. lépésben válassza ki **slotAppService** először, és válassza **slotAppServiceSlotOne** a második alkalommal.

1. A főmenü az Azure portál, válassza ki a **erőforráscsoportok**.

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
 
1. Váltás a böngészőlapon futtató **slotAppService** (az alkalmazás és a kék lap). 

1. Térjen vissza az Azure portál egy külön lapján.

1. Nyissa meg a felhő rendszerhéjat.

1. Lépjen a **clouddrive/csere** könyvtár.

    ```bash
    cd clouddrive/swap
    ```

1. A vi szerkesztő használatával hozzon létre egy fájlt `swap.tf`.

    ```bash
    vi swap.tf
    ```

1. Adja meg a beszúrási módban kiválasztásával a kulcsot.

1. Az alábbi kód beillesztése a szerkesztőbe:

    ```JSON
    # Configure the Azure provider
    provider "azurerm" { }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Válassza ki az Esc billentyűt az insert módjából való kilépéshez.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

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

1. Terraform befejezése után áttelepíteni a forráskörnyezetból a tárhelyek, a böngészőben lépjen vissza, amely megjeleníti a **slotAppService** webalkalmazás, és frissítse az oldalt. 

A webalkalmazás a **slotAppServiceSlotOne** tárolóhely átmeneti rendelkezik az éles tárolóhelyre lett cserél, és most zöld megjelenítése. 

![Az üzembe helyezési rendelkezik lett cserélve.](./media/terraform-slot-walkthru/slots-swapped.png)

Az alkalmazás eredeti éles verzióját visszaállításához alkalmazza újra a létrehozott Terraform tervet a `swap.tf` konfigurációs fájlt.

```bash
terraform apply
```

Miután az alkalmazás van cserélve, tekintse meg az eredeti konfigurációt.