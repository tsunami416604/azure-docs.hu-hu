---
title: Oktatóanyag – Infrastruktúra kiépítése az Azure telepítési bővítőhelyekkel a Terraform használatával
description: Ebben az oktatóanyagban a Terraform ot használja az Azure-szolgáltató telepítési bővítőhelyekkel
keywords: azure devops terraform telepítési tárolóhelyek
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: ddd4d84ee8bf4ab1e90dd68da185cdd9075fe1e0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78943485"
---
# <a name="tutorial-provision-infrastructure-with-azure-deployment-slots-using-terraform"></a>Oktatóanyag: Infrastruktúra kiépítése az Azure telepítési bővítőhelyekkel a Terraform használatával

Az [Azure üzembehelyezési pontjainak](/azure/app-service/deploy-staging-slots) használatával válthat az alkalmazások különböző verziói között. Ennek köszönhetően minimalizálhatja a nem működő üzemelő példányok okozta hatásokat. 

Ez a cikk egy példát mutat be az üzembehelyezési pontok használatára, és végigvezeti két alkalmazás a GitHub és az Azure segítségével való üzembe helyezésére. Az egyik alkalmazás egy éles üzemi, a másik pedig egy előkészítési ponton fut. (A "termelés" és az "előkészítés" nevek tetszőlegesek. Ezek lehetnek, ami megfelel a forgatókönyv.) A központi telepítési tárolóhelyek konfigurálása után a Terraform segítségével szükség szerint cserélhet a két tárolóhely között.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **GitHub-fiók**: A tesztelési GitHub-adattár leágaztatásához és használatához szüksége lesz egy [GitHub](https://www.github.com)-fiókra.

## <a name="create-and-apply-the-terraform-plan"></a>A Terraform-terv létrehozása és alkalmazása

1. Keresse fel az [Azure Portalt](https://portal.azure.com).

1. Nyissa meg [az Azure Cloud Shell](/azure/cloud-shell/overview)t. Ha még nem választott ki környezetet, válassza a **Bash** környezetet.

    ![Cloud Shell-parancssor](./media/terraform-slot-walkthru/azure-portal-cloud-shell-button-min.png)

1. Lépjen be a `clouddrive` könyvtárba.

    ```bash
    cd clouddrive
    ```

1. Hozzon létre egy `deploy` nevű könyvtárat.

    ```bash
    mkdir deploy
    ```

1. Hozzon létre egy `swap` nevű könyvtárat.

    ```bash
    mkdir swap
    ```

1. Az `ls` Bash-paranccsal ellenőrizheti, hogy mindkét könyvtár sikeresen létrejött-e.

    ![A Cloud Shell a könyvtárak létrehozása után](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. Lépjen be a `deploy` könyvtárba.

    ```bash
    cd deploy
    ```

1. Hozzon létre egy `deploy.tf` nevű fájlt a Cloud Shellben.

    ```bash
    code deploy.tf
    ```

1. Másolja az alábbi kódot a szerkesztőbe:

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { 
        # The "feature" block is required for AzureRM provider 2.x. 
        # If you are using version 1.x, the "features" block is not allowed.
        version = "~>2.0"
        features {}
    }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
        app_service_name    = azurerm_app_service.slotDemo.name
    }
    ```

1. Mentse a fájlt (**&lt;Ctrl>S),** és lépjen ki a szerkesztőből (**&lt;Ctrl>Q**).

1. A fájl létrehozását követően ellenőrizze annak tartalmát.

    ```bash
    cat deploy.tf
    ```

1. Inicializálja a Terraformot.

    ```bash
    terraform init
    ```

1. Hozza létre a Terraform-tervet.

    ```bash
    terraform plan
    ```

1. Foglalja le a `deploy.tf` konfigurációs fájlban definiált erőforrásokat. (A művelet megerősítéséhez írja be a parancssorba a `yes` parancsot.)

    ```bash
    terraform apply
    ```

1. Zárja be a Cloud Shell-ablakot.

1. Az Azure Portalon főmenüjében válassza az **Erőforráscsoportok** lehetőséget.

    ![„Erőforráscsoportok” kiválasztva a portálon](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. Az **Erőforráscsoportok** lapon válassza a **slotDemoResourceGroup** lehetőséget.

    ![A Terraform által létrehozott erőforráscsoport](./media/terraform-slot-walkthru/resource-group.png)

Ekkor látható a Terraform által létrehozott összes erőforrás.

![A Terraform által létrehozott erőforrások](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>A tesztprojekt leágaztatása

Az üzembehelyezési pontok létrehozásának és használatának teszteléséhez először le kell ágaztatnia a tesztprojektet a GitHubról.

1. Lépjen az [awesome-terraform adattárra a GitHubon](https://github.com/Azure/awesome-terraform).

1. Ágaztassa le az **awesome-terraform** adattárat.

    ![Az awesome-terraform GitHub-adattár leágaztatása](./media/terraform-slot-walkthru/fork-repo.png)

1. A környezet leágaztatásához kövesse a megjelenő utasításokat.

## <a name="deploy-from-github-to-your-deployment-slots"></a>Üzembe helyezés a GitHubról az üzembehelyezési pontokra

A tesztprojektadattár leágaztatását követően az üzembehelyezési pontokat a következő lépések végrehajtásával konfigurálhatja:

1. Az Azure Portalon főmenüjében válassza az **Erőforráscsoportok** lehetőséget.

1. Válassza a **slotDemoResourceGroup** lehetőséget.

1. Válassza a **slotAppService** lehetőséget.

1. Válassza az **Üzembehelyezési beállítások** lehetőséget.

    ![App Service-erőforrás üzembehelyezési beállításai](./media/terraform-slot-walkthru/deployment-options.png)

1. Az **Üzembehelyezési beállítás** lapon kattintson a **Forrás kiválasztása**, majd a **GitHub** elemre.

    ![Üzembehelyezési forrás kiválasztása](./media/terraform-slot-walkthru/select-source.png)

1. Miután az Azure létrehozza a kapcsolatot, és megjeleníti az összes lehetőséget, válassza az **Engedélyezés** lehetőséget.

1. Az **Engedélyezés** lapon kattintson az **Engedélyezés** gombra, és adja meg az Azure által a GitHub-fiók eléréséhez igényelt hitelesítő adatokat. 

1. Miután az Azure ellenőrizte a GitHub hitelesítő adatait, egy üzenet tájékoztatja, hogy az engedélyezési folyamat befejeződött. Az **OK** gombra kattintva zárja be az **Engedélyezés** lapot.

1. Kattintson a **Szervezet kiválasztása** elemre, és válassza ki a szervezetet.

1. Kattintson a **Projekt kiválasztása** elemre.

1. A **Projekt kiválasztása** lapon válassza ki az **awesome-terraform** projektet.

    ![Az awesome-terraform projekt kiválasztva](./media/terraform-slot-walkthru/choose-project.png)

1. Kattintson az **Ág kiválasztása** elemre.

1. Az **Ág kiválasztása** lapon válassza a **fő** elemet.

    ![A főág kiválasztva](./media/terraform-slot-walkthru/choose-branch-master.png)

1. Az **Üzembehelyezési beállítás** lapon kattintson az **OK** gombra.

Ezen a ponton üzembe helyezte az éles tárolóhelyet. Az átmeneti tárolóhely üzembe helyezéséhez hajtsa végre az előző lépéseket a következő módosításokkal:

- A 3. lépésben válassza a **slotAppServiceSlotOne** erőforrást.

- A 13. lépésben a főág helyett válassza a munkaágat.

    ![A munkaág kiválasztva](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>Az alkalmazás üzemelő példányainak tesztelése

Az előző szakaszokban két pontot (**slotAppService** és **slotAppServiceSlotOne**) hoztunk létre a különböző GitHub-ágakról való üzembe helyezéshez. Lássuk a webalkalmazások előnézetét, hogy ellenőrizhessük, sikeresen települtek-e.

1. Az Azure Portalon főmenüjében válassza az **Erőforráscsoportok** lehetőséget.

1. Válassza a **slotDemoResourceGroup** lehetőséget.

1. Kattintson a **slotAppService** vagy a **slotAppServiceSlotOne** elemre.

1. Az áttekintő oldalon kattintson az **URL** elemre.

    ![Az alkalmazás megjelenítése az áttekintő oldal URL elemére kattintva](./media/terraform-slot-walkthru/resource-url.png)

1. A kiválasztott alkalmazástól függően a következő eredmények jelennek meg:
    - **slotAppService** webapp - Kék oldal a **Slot Demo App 1**oldalcímmel. 
    - **slotAppServiceSlotOne** webapp - Zöld oldal a **Slot Demo App 2**oldalcímével.

    ![Az alkalmazások megfelelő üzembe helyezésének ellenőrzése az előnézeteik megtekintésével](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>Váltás a két üzembehelyezési pont között

A két üzembe helyezési tárolóhely cseréjének teszteléséhez tegye a következő lépéseket:
 
1. Váltson a böngésző **slotAppService** alkalmazást (a kék oldalt) megjelenítő lapjára. 

1. Egy új lapon lépjen vissza az Azure Portalra.

1. Nyissa meg a Cloud Shellt.

1. Váltson a **clouddrive/swap** könyvtárra.

    ```bash
    cd clouddrive/swap
    ```

1. Hozzon létre egy `swap.tf` nevű fájlt a Cloud Shellben.

    ```bash
    code swap.tf
    ```

1. Másolja az alábbi kódot a szerkesztőbe:

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { 
        # The "feature" block is required for AzureRM provider 2.x. 
        # If you are using version 1.x, the "features" block is not allowed.
        version = "~>2.0"
        features {}
    }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Mentse a fájlt (**&lt;Ctrl>S),** és lépjen ki a szerkesztőből (**&lt;Ctrl>Q**).

1. Inicializálja a Terraformot.

    ```bash
    terraform init
    ```

1. Hozza létre a Terraform-tervet.

    ```bash
    terraform plan
    ```

1. Foglalja le a `swap.tf` konfigurációs fájlban definiált erőforrásokat. (A művelet megerősítéséhez írja be a parancssorba a `yes` parancsot.)

    ```bash
    terraform apply
    ```

1. Miután a Terraform kicserélte a bővítőhelyeket, térjen vissza a böngészőbe. Frissítse az oldalt. 

A **slotAppServiceSlotOne** előkészítési ponton lévő webalkalmazás le lett cserélve az éles üzembehelyezési pontra, és most zöld színnel jelenik meg. 

![Az üzembehelyezési pontok felcserélve](./media/terraform-slot-walkthru/slots-swapped.png)

Az alkalmazás eredeti éles üzemi verziójára való visszaálláshoz alkalmazza újra a `swap.tf` konfigurációs fájlból létrehozott Terraform-tervet.

```bash
terraform apply
```

Az alkalmazás cseréjét követően az eredeti konfiguráció látható.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [További információ a Terraform azure-beli használatáról](/azure/terraform)