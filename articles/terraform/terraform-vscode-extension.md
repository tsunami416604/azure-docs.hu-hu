---
title: A Visual Studio Code Azure Terraform bővítményének telepítése és használata
description: Itt elsajátíthatja a Visual Studio Code Azure Terraform bővítményének telepítését és használatát.
services: terraform
ms.service: terraform
keywords: terraform, azure, devops, visual studio code, bővítmény
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/31/2018
ms.openlocfilehash: be0140606302335884d62ba60c875a049949b495
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353050"
---
# <a name="install-and-use-the-azure-terraform-visual-studio-code-extension"></a>A Visual Studio Code Azure Terraform bővítményének telepítése és használata

A Microsoft Azure Terraform Visual Studio Code-bővítmény segítségével a fejlesztők hatékonyabban kódolhatják, tesztelhetik és használhatják a Terraformot az Azure-ban. A bővítmény támogatja a Terraform-parancsokat, megjeleníti az erőforrások diagramjait, valamint biztosítja a Cloud Shell integrációját a Visual Studio Code-ban.

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Az Azure-erőforrások kiépítésének automatizálása és egyszerűsítése a Terraform használatával.
> * Az Azure-hoz készült Microsoft Terraform Visual Studio Code-bővítmény telepítése és használata.
> * Terraform-tervek létrehozása, megtervezése és végrehajtása a Visual Studio Code használatával.

## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Terraform**: [Telepítse és konfigurálja a Terraformot](/azure/virtual-machines/linux/terraform-install-configure).

- **Visual Studio Code**: Telepítse a [Visual Studio Code](https://code.visualstudio.com/download) a környezetnek megfelelő verzióját.

## <a name="prepare-your-dev-environment"></a>A fejlesztési környezet előkészítése

### <a name="install-git"></a>A Git telepítése

A cikkben foglalt gyakorlatok végrehajtásához [telepítenie kell a Gitet](https://git-scm.com/).

### <a name="install-hashicorp-terraform"></a>A HashiCorp Terraform telepítése

Kövesse a HashiCorp [a Terraform telepítését](https://www.terraform.io/intro/getting-started/install.html) bemutató weboldalán található utasításokat:

- A Terraform letöltése
- A Terraform telepítése
- A Terraform megfelelő telepítésének ellenőrzése

>[!Tip]
>Mindenképp hajtsa végre a PATH rendszerváltozó beállítására vonatkozó utasításokat.

### <a name="install-nodejs"></a>A Node.js telepítése

A Terraform Cloud Shellben való használatához [telepítenie kell a Node.js](https://nodejs.org/) 6.0-s vagy újabb verzióját.

>[!NOTE]
>A Node.js telepítésének ellenőrzéséhez nyisson meg egy terminálablakot, és írja be a `node -v` parancsot.

### <a name="install-graphviz"></a>A GraphViz telepítése

A Terraform vizualizációs funkciójának használatához [telepítenie kell a GraphVizt](http://graphviz.org/).

>[!NOTE]
>A GraphViz telepítésének ellenőrzéséhez nyisson meg egy terminálablakot, és írja be a `dot -V` parancsot.

### <a name="install-the-azure-terraform-visual-studio-code-extension"></a>A Visual Studio Code Azure Terraform bővítményének telepítése

1. Indítsa el a Visual Studio Code-ot.

1. Kattintson az **Extensions** (Bővítmények) gombra.

    ![Extensions (Bővítmények) gomb](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

1. A **Search Extensions in Marketplace** (Bővítmények keresése a Marketplace-en) mezőben keressen rá az Azure Terraform-bővítményre:

    ![Visual Studio Code-bővítmények keresése a Marketplace-en](media/terraform-vscode-extension/tf-search-extensions.png)

1. Válassza az **Install** (Telepítés) lehetőséget.

    >[!NOTE]
    >Az Azure Terraform-bővítménynél az **Install** (Telepítés) elemre kattintva a Visual Studio Code automatikusan telepíti az Azure-fiók bővítményét. Az Azure-fiók az Azure Terraform-bővítmény függő fájlja, és a bővítmény azon keresztül hajtja végre az Azure-előfizetés hitelesítését és az Azure-ral kapcsolatos kódkiterjesztést.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>A Terraform-bővítmény Visual Studio Code-ban való telepítésének ellenőrzése

1. Kattintson az **Extensions** (Bővítmények) gombra.

1. Írja be az `@installed` kifejezést a keresőmezőbe.

    ![Telepített bővítmények](media/terraform-vscode-extension/tf-installed-extensions.png)

Az Azure Terraform-bővítmény megjelenik a telepített bővítmények listájában.

![Telepített Terraform-bővítmények](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

Az összes támogatott Terraform-parancsot futtathatja a Cloud Shell-környezetben a Visual Studio Code-ból.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>1. gyakorlat: Alapvető Terraform-parancsok áttekintése

Ebben a gyakorlatban egy olyan alapszintű Terraform konfigurációs fájlt hozunk létre és hajtunk végre, amely egy Azure-erőforráscsoport kiosztását végzi.

### <a name="prepare-a-test-plan-file"></a>Tesztelési tervfájl előkészítése

1. A Visual Studio Code-ban kattintson a menüsáv **File (Fájl) -> New File (Új fájl)** elemére.

1. A böngészőjében lépjen a [Terraform azurerm_resource_group oldalra](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#), és másolja az **Example Usage** (Gyakorlati példa) kódblokkban lévő kódot a vágólapra:

    ![Gyakorlati példa](media/terraform-vscode-extension/tf-azurerm-resource-group-example-usage.png)

1. Illessze be a másolt kódot a Visual Studio Code-ban létrehozott új fájlba.

    ![Gyakorlati példakód beillesztése](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >Módosíthatja az erőforráscsoport **név** értékét, azonban a névnek egyedinek kell lennie az Azure-előfizetésen belül.

1. A menüsávon kattintson a **File (Fájl) > Save As (Mentés másként)** elemre.

1. A **Save As** (Mentés másként) párbeszédablakban lépjen a kívánt helyre, majd válassza a **New folder** (Új mappa) lehetőséget. (Módosítsa az új mappa nevét valami beszédesebbre, mint az *Új mappa*.)

    >[!NOTE]
    >Ebben a példában a mappának a TERRAFORM-TEST-PLAN (TERRAFORM-TESZT-TERV) nevet adjuk.

1. Ellenőrizze, hogy az új mappa ki van emelve-e (kiválasztva), majd válassza az **Open** (Megnyitás) lehetőséget.

1. A **Save As** (Mentés másként) párbeszédpanelen cserélje le a fájl alapértelmezett nevét a *main.tf* névre.

    ![Mentés main.tf néven](media/terraform-vscode-extension/tf-save-as-main.png)

1. Kattintson a **Mentés** gombra.
- A menüsávon kattintson a **File (Fájl) > Open Folder (Mappa megnyitása)** elemre. Keresse meg a létrehozott új mappát, majd válassza ki.

### <a name="run-terraform-init-command"></a>Az *init* Terraform-parancs futtatása

1. Indítsa el a Visual Studio Code-ot.

1. A Visual Studio Code menüsávján válassza a **File (Fájl) > Open Folder... (Mappa megnyitása...)** lehetőséget, és válassza ki a *main.tf* fájlt.

    ![main.tf fájl](media/terraform-vscode-extension/tf-main-tf.png)

1. A menüsávon kattintson a **View (Nézet) > Command Palette... (Parancskatalógus...) > Azure Terraform: Init** elemre.

1. Amikor a megerősítési üzenet megjelenik, kattintson az **OK** gombra.

    ![Meg kívánja nyitni a Cloud Shellt?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

1. Amikor egy új mappából az első alkalommal nyitja meg a Cloud Shellt, a rendszer megkéri, hogy állítsa be a webalkalmazást. Válassza az **Open** (Megnyitás) elemet.

    ![A Cloud Shell első indítása](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

1. Megnyílik az Azure Cloud Shell üdvözlőképernyője. Válassza a Bash vagy a PowerShell lehetőséget.

    ![Az Azure Cloud Shell üdvözlőképernyője](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >Ebben a példában a Bash (Linux) lehetőséget választottuk.

1. Ha még nem állította be az Azure-tárfiókot, a következő képernyő jelenik meg. Kattintson a **Create storage** (Tároló létrehozása) gombra.

    ![Nincs csatlakoztatott tároló](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. Az Azure Cloud Shell az előzőleg kiválasztott rendszerhéjban indul el, és megjeleníti az imént létrehozott felhőalapú meghajtó adatait.

    ![A felhőalapú meghajtó létrejött](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

1. Ekkor kiléphet a Cloud Shellből

1. A menüsávon kattintson a **View (Nézet)** > **Command Palette (Parancskatalógus)** > **Azure Terraform: init** elemre.

    ![A Terraform sikeresen inicializálva lett](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>A terv vizualizációja

Az oktatóanyag korábbi szakaszában telepítettük a GraphVizt. A Terraform a GraphViz használatával elkészíti egy konfiguráció vagy egy végrehajtási terv vizuális megjelenítését. A Visual Studio Code Azure Terraform-bővítménye ezt a szolgáltatást a *visualize* paranccsal valósítja meg.

- A menüsávon kattintson a **View (Nézet) > Command Palette (Parancskatalógus) > Azure Terraform: Visualize** elemre.

    ![A terv vizualizációja](media/terraform-vscode-extension/tf-graph.png)

### <a name="run-terraform-plan-command"></a>A *plan* Terraform-parancs futtatása

A *plan* Terraform-parancs segítségével ellenőrizheti, hogy egy adott módosításhalmazt megvalósító végrehajtási terv valóban a kívánt eredményt éri-e el.

>[!NOTE]
>A *plan* Terraform-parancs ténylegesen nem módosítja az Azure-erőforrásokat. A tervben foglalt módosítások tényleges végrehajtásához az *apply* Terraform-parancsot használjuk.

- A menüsávon kattintson a **View (Nézet)** > **Command Palette (Parancskatalógus)** > **Azure Terraform: plan** elemre.

    ![Terraform plan](media/terraform-vscode-extension/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>Az *apply* Terraform-parancs futtatása

Ha elégedett a *plan* Terraform-parancs eredményeivel, futtathatja az *apply* parancsot.

1. A menüsávon kattintson a **View (Nézet)** > **Command Palette (Parancskatalógus)** > **Azure Terraform: apply** elemre.

    ![Terraform apply](media/terraform-vscode-extension/tf-terraform-apply.png)

1. Írja be a `yes` (igen) kifejezést.

    ![Terraform apply yes](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>A Terraform-terv végrehajtásának ellenőrzése

Az új Azure-erőforráscsoport sikeres létrehozásának ellenőrzése:

1. Nyissa meg az Azure Portalt.

1. A bal oldali navigációs panelen válassza az **Erőforráscsoportok** lehetőséget.

    ![Az új erőforrás ellenőrzése](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

Az új erőforráscsoportnak meg kell jelennie a **NÉV** oszlopban.

>[!NOTE]
>Az Azure Portal ablakát nyitva hagyhatja, mert a következő lépésben használni fogjuk.

### <a name="run-terraform-destroy-command"></a>A *destroy* Terraform-parancs futtatása

1. A menüsávon kattintson a **View (Nézet)** > **Command Palette (Parancskatalógus)** > **Azure Terraform: destroy** elemre.

    ![Terraform destroy](media/terraform-vscode-extension/tf-terraform-destroy.png)

1. Írja be a *yes* (igen) kifejezést.

    ![Terraform destroy yes](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>Az erőforráscsoport törlésének ellenőrzése

Az új erőforráscsoport Terraform általi sikeres törlésének ellenőrzése:

1. Kattintson a **Frissítés** gombra az Azure Portal **Erőforráscsoportok** lapján.

1. Az erőforráscsoport már nem szerepel a listában.

    ![Az erőforráscsoport törlésének ellenőrzése](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>2. gyakorlat: Terraform *compute* modul

Ebből a gyakorlatból megtudhatja, hogyan töltheti be a Terraform *compute* modult a Visual Studio Code-környezetbe.

### <a name="clone-the-terraform-azurerm-compute-module"></a>A terraform-azurerm-compute modul klónozása

1. [Ezen a hivatkozáson](https://github.com/Azure/terraform-azurerm-compute) érheti el a Terraform Azure Rm Compute modult a GitHubon.

1. Válassza a **Clone or download** (Klónozás vagy letöltés) lehetőséget.

    ![Klónozás vagy letöltés](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >Ebben a példában a mappa neve *terraform-azurerm-compute*.

### <a name="open-the-folder-in-visual-studio-code"></a>A mappa megnyitása a Visual Studio Code-ban

1. Indítsa el a Visual Studio Code-ot.

1. A menüsávon kattintson a **File (Fájl) > Open Folder (Mappa megnyitása)** elemre, majd lépjen az előző lépésben létrehozott mappára, és válassza ki.

    ![A terraform-azurerm-compute mappa](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>A Terraform inicializálása

Mielőtt használhatná a Terraform-parancsokat a Visual Studio Code-ból, le kell töltenie két Azure-szolgáltató, a random és az azurerm beépülő moduljait.

1. A Visual Studio Code IDE Terminal (Terminál) paneljén írja be a `terraform init` parancsot.

    ![A terraform init parancs](media/terraform-vscode-extension/tf-terraform-init-command.png)

1. Írja be az `az login` parancsot, nyomja le az **<Enter** billentyűt, és kövesse a képernyőn megjelenő utasításokat.

### <a name="module-test-lint"></a>Modulteszt: *lint*

1. A menüsávon kattintson a **View (Nézet) > Command Palette (Parancskatalógus) > Azure Terraform: Execute Test (Azure Terraform: Teszt végrehajtása)** elemre.

1. A lehetséges teszttípusok listáján válassza a **lint** lehetőséget.

    ![Teszttípus kiválasztása](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

1. Amikor a megerősítési üzenet megjelenik, kattintson az **OK** gombra, és kövesse a képernyőn megjelenő utasításokat.

    ![Valóban megnyitja a CloudShellt?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>A **lint** vagy a **teljes körű** tesztek végrehajtásakor az Azure egy tárolószolgáltatás használatával kioszt egy tesztgépet a tényleges teszt végrehajtására. Ezért a teszteredmények visszaadása több percet is igénybe vehet.

Rövid idő múlva egy, az alábbihoz hasonló felsorolás jelenik meg a Terminal (Terminál) panelen:

![Lint teszt eredményei](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="module-test-end-to-end"></a>Modulteszt: *teljes körű*

1. A menüsávon kattintson a **View (Nézet) > Command Palette (Parancskatalógus) > Azure Terraform: Execute Test (Azure Terraform: Teszt végrehajtása)** elemre.

1. A lehetséges teszttípusok listáján válassza az **end to end** (teljes körű) lehetőséget.

    ![Teszttípus kiválasztása](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

1. Amikor a megerősítési üzenet megjelenik, kattintson az **OK** gombra, és kövesse a képernyőn megjelenő utasításokat.

    ![Valóban megnyitja a CloudShellt?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>A **lint** vagy a **teljes körű** tesztek végrehajtásakor az Azure egy tárolószolgáltatás használatával kioszt egy tesztgépet a tényleges teszt végrehajtására. Ezért a teszteredmények visszaadása több percet is igénybe vehet.

Rövid idő múlva egy, az alábbihoz hasonló felsorolás jelenik meg a Terminal (Terminál) panelen:

![Teljes körű teszt eredményei](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Az Azure-hoz (és más támogatott szolgáltatókhoz) elérhető Terraform-modulok listája](https://registry.terraform.io/)