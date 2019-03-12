---
title: A Terraform és az Azure-szolgáltatók üzembehelyezési pontjai
description: Oktatóanyag a Terraform az Azure-szolgáltatók üzembehelyezési pontjaival való használatáról
services: terraform
ms.service: azure
keywords: terraform, devops, virtuális gép, Azure, üzembehelyezési pontok
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 4/05/2018
ms.openlocfilehash: c1ff166431ed75ade1192cdefadb7928943ab2b2
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771293"
---
# <a name="use-terraform-to-provision-infrastructure-with-azure-deployment-slots"></a>A Terraform használata infrastruktúra kiépítésére az Azure üzembehelyezési pontjaival

Az [Azure üzembehelyezési pontjainak](/azure/app-service/deploy-staging-slots) használatával válthat az alkalmazások különböző verziói között. Ennek köszönhetően minimalizálhatja a nem működő üzemelő példányok okozta hatásokat. 

Ez a cikk egy példát mutat be az üzembehelyezési pontok használatára, és végigvezeti két alkalmazás a GitHub és az Azure segítségével való üzembe helyezésére. Az egyik alkalmazás egy éles üzemi, a másik pedig egy előkészítési ponton fut. (Az „éles üzemi” és az „előkészítési” elnevezések használata tetszőleges, és az adott forgatókönyvnek megfelelően bármilyen elnevezés használható.) Az üzembehelyezési pontok konfigurálását követően a Terraform használatával igény szerint válthat a két pont között.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.

- **GitHub-fiók**: Kell egy [GitHub](http://www.github.com) fiók elágaztatását, és a teszt GitHub-adattárban.

## <a name="create-and-apply-the-terraform-plan"></a>A Terraform-terv létrehozása és alkalmazása

1. Keresse fel az [Azure Portalt](http://portal.azure.com).

1. Nyissa meg az [Azure Cloud Shellt](/azure/cloud-shell/overview). Ha még nem választott ki környezetet, válassza a **Bash** környezetet.

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

1. A [VI-szerkesztő](https://www.debian.org/doc/manuals/debian-tutorial/ch-editor.html) használatával hozzon létre egy fájlt `deploy.tf` néven. Ez a fájl tartalmazza majd a [Terraform-konfigurációt](https://www.terraform.io/docs/configuration/index.html).

    ```bash
    vi deploy.tf
    ```

1. Az I billentyű lenyomásával lépjen beszúrási módba.

1. Másolja az alábbi kódot a szerkesztőbe:

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

1. Az Esc billentyűvel lépjen ki a beszúrás módból.

1. Mentse a fájlt, és lépjen ki a VI-szerkesztőből a következő parancs megadásával:

    ```bash
    :wq
    ```

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

Ezen a ponton üzembe helyezte az éles üzemi pontot. Az előkészítési pont üzembe helyezéséhez hajtsa végre az ebben a szakaszban eddig ismertetett lépéseket a következő módosításokkal:

- A 3. lépésben válassza a **slotAppServiceSlotOne** erőforrást.

- A 13. lépésben a főág helyett válassza a munkaágat.

    ![A munkaág kiválasztva](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>Az alkalmazás üzemelő példányainak tesztelése

Az előző szakaszokban két pontot (**slotAppService** és **slotAppServiceSlotOne**) hoztunk létre a különböző GitHub-ágakról való üzembe helyezéshez. Lássuk a webalkalmazások előnézetét, hogy ellenőrizhessük, sikeresen települtek-e.

A következő lépéseket kétszer hajtsa végre. A 3. lépésben első alkalommal a **slotAppService**, második alkalommal a **slotAppServiceSlotOne** lehetőséget válassza.

1. Az Azure Portalon főmenüjében válassza az **Erőforráscsoportok** lehetőséget.

1. Válassza a **slotDemoResourceGroup** lehetőséget.

1. Kattintson a **slotAppService** vagy a **slotAppServiceSlotOne** elemre.

1. Az áttekintő oldalon kattintson az **URL** elemre.

    ![Az alkalmazás megjelenítése az áttekintő oldal URL elemére kattintva](./media/terraform-slot-walkthru/resource-url.png)

> [!NOTE]
> Néhány percet igénybe vehet, amíg az Azure a GitHubról összeállítja és üzembe helyezi a webhelyet.
>
>

A **slotAppService** webalkalmazás esetében egy kék oldal jelenik meg a **Slot Demo App 1** (1. pontbemutató alkalmazás) oldalcímmel. A **slotAppServiceSlotOne** webalkalmazás esetében egy zöld oldal jelenik meg a **Slot Demo App 2** (2. pontbemutató alkalmazás) oldalcímmel.

![Az alkalmazások megfelelő üzembe helyezésének ellenőrzése az előnézeteik megtekintésével](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>Váltás a két üzembehelyezési pont között

A kér üzembehelyezési pont közti váltás teszteléséhez hajtsa végre a következő lépéseket:
 
1. Váltson a böngésző **slotAppService** alkalmazást (a kék oldalt) megjelenítő lapjára. 

1. Egy új lapon lépjen vissza az Azure Portalra.

1. Nyissa meg a Cloud Shellt.

1. Váltson a **clouddrive/swap** könyvtárra.

    ```bash
    cd clouddrive/swap
    ```

1. A VI-szerkesztő használatával hozzon létre egy fájlt `swap.tf` néven.

    ```bash
    vi swap.tf
    ```

1. Az I billentyű lenyomásával lépjen beszúrási módba.

1. Másolja az alábbi kódot a szerkesztőbe:

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

1. Az Esc billentyűvel lépjen ki a beszúrás módból.

1. Mentse a fájlt, és lépjen ki a VI-szerkesztőből a következő parancs megadásával:

    ```bash
    :wq
    ```

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

1. Miután a Terraform befejezte a pontok felcserélését, lépjen vissza a böngésző **slotAppService** webalkalmazást megjelenítő lapjára, és frissítse az oldalt. 

A **slotAppServiceSlotOne** előkészítési ponton lévő webalkalmazás le lett cserélve az éles üzembehelyezési pontra, és most zöld színnel jelenik meg. 

![Az üzembehelyezési pontok felcserélve](./media/terraform-slot-walkthru/slots-swapped.png)

Az alkalmazás eredeti éles üzemi verziójára való visszaálláshoz alkalmazza újra a `swap.tf` konfigurációs fájlból létrehozott Terraform-tervet.

```bash
terraform apply
```

Az alkalmazás cseréjét követően az eredeti konfiguráció látható.