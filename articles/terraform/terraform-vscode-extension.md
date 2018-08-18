---
title: Az Azure Terraform VS Code-bővítménnyel |} A Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan telepítheti és használhatja a Terraform-bővítmény a Visual Studio Code.
keywords: a terraform, a devops, a virtuális gép, azure
author: v-mavick
ms.author: v-mavick
ms.date: 08/14/2018
ms.topic: article
ms.prod: vs-code
ms.openlocfilehash: 0c88879faae100372055479ad4edb8c36d8f557d
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190229"
---
# <a name="azure-terraform-vs-code-extension"></a>Az Azure Terraform VS Code-bővítmény

A Microsoft Azure Terraform Visual Studio Code (a VS Code) bővítmény célja a fejlesztői hatékonyság növelésére készítése, tesztelése és és az Azure Terraform használatával. A bővítmény megadja a Terraform parancs támogatása, a erőforrás graph megjelenítési és a cloud Shell-integráció a VS Code belül.

## <a name="what-you-do"></a>TEENDŐ

- Telepítse a nyílt forráskódú HashiCorp Terraform végrehajtható fájlt a gépén.
- Telepítse a Terraform VS Code-bővítmény az Azure-hoz a helyi VS Code telepítése.

## <a name="what-you-learn"></a>Ismertetett témák

Ez az oktatóanyag bemutatja:

- A Terraform hogyan automatizálhatja és egyszerűsítheti az Azure-szolgáltatások üzembe helyezése.
- Hogyan kell telepíteni, és a Microsoft Terraform VS Code-bővítmény használata az Azure-szolgáltatásokhoz.
- Hogyan lehet írni, és hajtsa végre a Terraform tervek a VS Code használata.

## <a name="what-you-need"></a>Mi szükséges

- A Windows 10, Linux vagy macOS 10.10 + futtató számítógép.
- [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US).
- Aktív Azure-előfizetés. [Ingyenes 30 napos próba Microsoft Azure-fiók aktiválása](https://azure.microsoft.com/free/).
- Telepíteni kell a [Terraform](https://www.terraform.io/) nyílt forráskódú eszköz, a helyi gépen.
  
## <a name="prepare-your-dev-environment"></a>A fejlesztési környezet előkészítése

### <a name="install-git"></a>A Git telepítése

A cikkben szereplő gyakorlatok végrehajtásához kell [a Git telepítése](https://git-scm.com/).

### <a name="install-hashicorp-terraform"></a>A HashiCorp Terraform telepítése

Kövesse a HashiCorp [Terraform telepítése](https://www.terraform.io/intro/getting-started/install.html) weblapon, amely ismerteti:

- A Terraform letöltése
- A Terraform telepítése
- A Terraform ellenőrzése megfelelően telepítve.

>[!Tip]
>Ügyeljen arra, hogy kövesse az utasításokat vonatkozó beállítás az elérési út rendszerváltozóhoz.

### <a name="install-nodejs"></a>A Node.js telepítése

A Terraform használata a Cloud shellben, kell [a Node.js telepítéséhez](https://nodejs.org/) 6.0.

>[!NOTE]
>Annak ellenőrzéséhez, hogy telepítve van-e a Node.js, nyisson meg egy terminálablakot, és írja be: `node -v`

### <a name="install-graphviz"></a>GraphViz telepítése

A Terraform használatával jelenítheti meg a függvényt, meg kell [GraphViz telepítése](http://graphviz.org/).

>[!NOTE]
>Annak ellenőrzéséhez, hogy telepítve van-e a GraphViz, nyisson meg egy terminálablakot, és írja be: `dot -V`

### <a name="install-the-azure-terraform-vs-code-extension"></a>Az Azure Terraform VS Code-bővítmény telepítése:

1. Indítsa el a VS Code.
2. Kattintson a **bővítmények** ikonra.

    ![Bővítmények gomb](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

3. Használja a **keresési bővítmények a Marketplace-en** szöveges mező használatával keresheti meg az Azure Terraform bővítmény:

    ![VS Code-bővítmények keresése a piactéren](media/terraform-vscode-extension/tf-search-extensions.png)

4. Kattintson az **Install** (Telepítés) gombra.

    >[!NOTE]
    >Amikor rákattint **telepítése** az Azure Terraform bővítmény esetében a VS Code automatikusan telepíti az Azure-fiókkiterjesztés. Azure-fiók az Azure Terraform bővítmény esetében végre Azure-előfizetés hitelesítés és Azure-hoz kapcsolódó code-bővítményeket használ, amely egy függőségi fájlt.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>A Terraform-bővítmény telepítve van-e a Visual Studio Code-ban

1. Kattintson a bővítmény ikonra.
2. Típus `@installed` szöveget a keresőmezőbe.

    ![Telepített bővítmények](media/terraform-vscode-extension/tf-installed-extensions.png)

Az Azure Terraform bővítmény telepített bővítmények listájában jelenik meg.

![A Terraform telepített bővítmények](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

Az összes támogatott Terraform parancsok a VS Code belül a Cloud Shell-környezet most már futtathatja.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>1. gyakorlat: Alapszintű Terraform parancsok segédlet

Ebben a gyakorlatban létrehozása, és hajtsa végre egy alapszintű Terraform konfigurációs fájlt, amely kiépíti egy új Azure-erőforráscsoportot.

### <a name="prepare-a-test-plan-file"></a>Tesztelési terv fájl előkészítése

1. A VS Code-ban válassza ki a **fájl > új fájl** a menüsoron.
2. Keresse meg a [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#) , és másolja a kódot a **példa használati** kódblokk:
3. Illessze be a másolt kód a VS Code-ban létrehozott új fájlt.

    ![Példa használati kód beillesztése](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >Előfordulhat, hogy módosítja a **neve** értékét az erőforráscsoport, de az Azure-előfizetéshez egyedinek kell lennie.

4. A menüsávban válassza **fájl > Mentés másként**.
5. Az a **Mentés másként** párbeszédpanelen keresse meg egy tetszőleges helyre, és kattintson a **új mappa**. (Tulajdonságánál, mint az új mappa nevének módosítása *új mappa*.)

    >[!NOTE]
    >Ebben a példában a mappa a TERRAFORM TESZTTERV neve.

6. Ellenőrizze, hogy az új mappa ki van emelve (kiválasztva), és kattintson a **nyílt**.
7. Az a **Mentés másként** párbeszédpanelen módosítsa a fájl alapértelmezett neve *main.tf*.

    ![Mentés másként main.tf](media/terraform-vscode-extension/tf-save-as-main.png)

8. Kattintson a **Save** (Mentés) gombra.
- A menüsávban válassza **fájl > mappa megnyitása**. Keresse meg és válassza ki a létrehozott új mappát.

### <a name="run-terraform-init-command"></a>Futtassa a Terraform *init* parancs

1. Indítsa el a Visual Studio Code-ot.
2. A VS Code menüsávban válassza **fájl > mappa megnyitása...**  , és keresse meg és válassza ki a *main.tf* fájlt.

    ![main.tf fájl](media/terraform-vscode-extension/tf-main-tf.png)

3. A menüsávban válassza **Nézet > Parancskatalógus... > az Azure Terraform: Init**.
4. Néhány pillanat múlva kérdésnél *nyissa meg a Cloud Shellben szeretné?* Kattintson az **OK** gombra.

    ![Nyissa meg a Cloud Shellben szeretné?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

5. A Cloud Shell egy új mappát, az első indításakor a rendszer kéri, a webalkalmazás beállítása. Kattintson a **nyílt**.

    ![A Cloud Shell az első indításkor](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

6. Üdvözli az Azure Cloud Shell lapon nyílik meg. Válassza ki a Bash vagy a PowerShell használatával.

    ![Üdvözli az Azure Cloud Shell](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >Ebben a példában a Bash (Linux) lett kiválasztva.

7. Ha már nem beállítva egy Azure storage-fiókot, a következő képernyő jelenik meg. Kattintson a **tároló létrehozása**.

    ![Nincs csatlakoztatott tároló](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. Az Azure Cloud Shell elindítja a rendszerhéj korábban kiválasztott és imént létrehozott az Ön számára a felhőalapú meghajtójukra adatait jeleníti meg.

    ![A felhőalapú meghajtójukra létrejött](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

9. Előfordulhat, hogy kilép a Cloud Shellben
10. A menüsávban válassza **nézet** > **Parancskatalógus** > **Azure Terraform: init**.

    ![A Terraform inicializálása sikeresen befejeződött](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>A terv megjelenítése

Ebben az oktatóanyagban a korábban telepített GraphViz. A Terraform használatával GraphViz vizuális megjelenítését, konfigurációs vagy végrehajtási terv készítése. Az Azure Terraform VS Code-bővítménnyel Ez a funkció használatával valósítja meg a *megjelenítése* parancsot.

- Az a **menüsoron**, jelölje be**Nézet > Parancskatalógus > az Azure Terraform: megjelenítése**.

    ![A terv megjelenítése](media/terraform-vscode-extension/tf-graph.png)

### <a name="run-terraform-plan-command"></a>Futtassa a Terraform *terv* parancs

A Terraform *terv* parancs segítségével ellenőrizze, hogy e módosítások egy készletének végrehajtási terv kívánt elvégzi.

>[!NOTE]
>A Terraform *terv* nem végezze el a módosításokat valós Azure-erőforrásaival. Valójában a módosításokat a csomagban foglalt, azt a Terraform használata *alkalmazása* parancsot.

- A menüsávban válassza **nézet** > **Parancskatalógus** > **Azure Terraform: terv**.

    ![A Terraform terv](media/terraform-vscode-extension/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>Futtassa a Terraform *alkalmazása* parancs

Folyamatban elégedett a Terraform eredményeit után *terv*, futtathatja a *alkalmazása* parancsot.

1. A menüsávban válassza **nézet** > **Parancskatalógus** > **Azure Terraform: alkalmazása**.

    ![A Terraform a alkalmazni](media/terraform-vscode-extension/tf-terraform-apply.png)

2. Típus *Igen*.

    ![A Terraform a alkalmazni Igen](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>Ellenőrizze, hogy a Terraform terv végre lett hajtva.

Megtekintheti, ha az új Azure-erőforráscsoport létrehozása sikeresen megtörtént:

1. Nyissa meg az Azure Portalt.
2. Válassza ki **erőforráscsoportok** a bal oldali navigációs ablaktáblán.

    ![Ellenőrizze az új erőforrás](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

Az új erőforráscsoportnak kell szerepelnie a **neve** oszlop.

>[!NOTE]
>Előfordulhat, hogy hagyja az Azure Portal ablak megnyitása most; fogjuk használni, a következő lépésben.

### <a name="run-terraform-destroy-command"></a>Futtassa a Terraform *megsemmisítése* parancs

1. A menüsávban válassza **nézet** > **Parancskatalógus** > **Azure Terraform: megsemmisítése**.

    ![A Terraform megszüntetése](media/terraform-vscode-extension/tf-terraform-destroy.png)

2. Típus *Igen*.

    ![A Terraform Igen megszüntetése](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>Ellenőrizze, hogy az erőforráscsoport megsemmisül volt

Ellenőrizze, hogy a Terraform sikerült megsemmisíteni, az új erőforráscsoport:

1. Kattintson a **frissítése** az Azure Portalon *erőforráscsoportok* lapot.
2. Az erőforráscsoport már nem jelennek meg.

    ![Erőforráscsoport volt megsemmisül ellenőrzése](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>2. gyakorlat: Terraform *számítási* modul

Ebben a gyakorlatban mutatja a Terraform betöltése *számítási* modul a VS Code-környezetbe.

### <a name="clone-the-terraform-azurerm-compute-module"></a>Klónozza a terraform-azurerm-compute-modul

1. Használat [ezt a hivatkozást](https://github.com/Azure/terraform-azurerm-compute) eléréséhez a Terraform az Azure Rm számítási modul a Githubon.
2. Kattintson a **Klónozás vagy letöltés**.

    ![Klónozás vagy letöltés](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >Ebben a példában a mappa neve volt *terraform-azurerm-számítási*.

### <a name="open-the-folder-in-vs-code"></a>Nyissa meg a mappát a VS Code-ban

1. Indítsa el a Visual Studio Code-ot.
2. Az a **menüsoron**, jelölje be **fájl > mappa megnyitása** , és keresse meg és válassza ki az előző lépésben létrehozott mappába.

    ![terraform-azurerm-compute-mappa](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>A Terraform inicializálása

Mielőtt elkezdhetné a VS Code belül a Terraform-parancsok használatával, a két Azure szolgáltatók töltse le a beépülő modulok: véletlenszerű és azurerm.

1. A VS Code integrált terminál ablakában írja be: `terraform init`

    ![a terraform init paranccsal](media/terraform-vscode-extension/tf-terraform-init-command.png)

2. Típus `az login` , és kövesse a képernyőn megjelenő utasításokat.

### <a name="module-test-lint"></a>A modul teszt: *lint*

1. Az a **menüsor**válassza **Nézet > Parancskatalógus > Azure Terraform: teszt végrehajtása**.
2. Teszt típusa lehetőségek listájából válassza ki a **lint**.

    ![Válassza ki a teszt típusa](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

3. Ha a rendszer kéri *szeretné nyissa meg a cloud Shell?* kattintson **OK** , és kövesse a képernyőn megjelenő utasításokat.

    ![Biztosan nyissa meg a cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Amikor végre, vagy a **lint** vagy **teljes körű** teszt, az Azure container szolgáltatást használ a tényleges teszt végrehajtásához teszt gép kiépítése. Ebből kifolyólag a teszt eredményei általában vissza kell több percig is eltarthat.

Néhány pillanat múlva lista a példához hasonlóak a Terminálszolgáltatások ablaktáblán jelenik meg:

![Lint terhelésiteszt-eredményei](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="module-test-end-to-end"></a>A modul teszt: *végpontok közötti*

1. Az a **menüsor**válassza **Nézet > Parancskatalógus > Azure Terraform: teszt végrehajtása**.
2. Teszt típusa lehetőségek listájából válassza ki a **teljes körű**.

    ![Válassza ki a teszt típusa](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

3. Ha a rendszer rákérdez *szeretné nyissa meg a cloud Shell?* kattintson **OK** , és kövesse a képernyőn megjelenő utasításokat.

    ![Biztosan nyissa meg a cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Amikor végre, vagy a **lint** vagy **teljes körű** teszt, az Azure container szolgáltatást használ a tényleges teszt végrehajtásához teszt gép kiépítése. Ebből kifolyólag a teszt eredményei általában vissza kell több percig is eltarthat.

Néhány pillanat múlva lista a példához hasonlóak a Terminálszolgáltatások ablaktáblán jelenik meg:

![Teljes körű terhelésiteszt-eredményei](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>További lépések

Amint láthatta Terraform egyszerűbbé teheti az Azure-szolgáltatásokhoz közvetlenül a Visual Studio Code belül kiépítése több szempontból. Most érdemes tekintse át ezeket az erőforrásokat:
- A [Terraform-modulok beállításjegyzéke](https://registry.terraform.io/) felsorolja az összes rendelkezésre álló Terraform-modulokkal, az Azure és az egyéb támogatott szolgáltatók számára.

Ezeket a modulokat mindegyike esetében biztosított az alábbi adatokat:

- A modul általános képességek és teljesítményjellemzőit leírása
- Egy példa a használatra
- Vizsgálati konfigurációkra, amelyek bemutatják, hogyan hozhat létre, futtassa, és minden egyes modul a helyi fejlesztői gépen vizsgálati
- Egy docker-fájlban, hogy hozhat létre, és a egy modul fejlesztőkörnyezet helyi futtatásához.
