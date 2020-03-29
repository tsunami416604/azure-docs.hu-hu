---
title: Azure Stream Analytics-feladat üzembe helyezése CI/CD npm csomag használatával
description: Ez a cikk bemutatja, hogyan használhatja az Azure Stream Analytics CI/CD npm csomagot a folyamatos integrációs és üzembe helyezési folyamat beállításához.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: deb6c2439cc84f196b7f42fd9f49d3ebfd057cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76962191"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>Azure Stream Analytics-feladat üzembe helyezése CI/CD npm csomag használatával 

Az Azure Stream Analytics CI/CD npm csomag használatával folyamatos integrációs és üzembe helyezési folyamatot állíthat be a Stream Analytics-feladatokhoz. Ez a cikk ismerteti, hogyan kell használni az npm csomag általában bármely CI/CD-rendszer, valamint az Azure Pipelines üzembe helyezésre vonatkozó konkrét utasításokat.

A PowerShell használatával történő üzembe helyezésről a [Resource Manager-sablonfájlés az Azure PowerShell központi telepítése című témakörben olvashat bővebben.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) Az [Erőforrás-kezelő sablonban](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)arról is olvashat, hogyan használható paraméterként egy objektum.

## <a name="build-the-vs-code-project"></a>A VS Code projekt létrehozása

Az **asa-streamanalytics-cicd** npm csomag használatával engedélyezheti az Azure Stream Analytics-feladatok folyamatos integrációját és üzembe helyezését. Az npm csomag biztosítja az eszközöket a [Stream Analytics Visual Studio Code projektek](quick-create-vs-code.md)Azure Resource Manager-sablonjainak létrehozásához. Windows, macOS és Linux rendszeren is használható a Visual Studio Code telepítése nélkül.

A [csomagot](https://www.npmjs.com/package/azure-streamanalytics-cicd) közvetlenül letöltheti, vagy [globálisan](https://docs.npmjs.com/downloading-and-installing-packages-globally) telepítheti a `npm install -g azure-streamanalytics-cicd` parancs segítségével. Ez az ajánlott megközelítés, amely egy Build-folyamat Build-folyamat egy PowerShell- vagy Azure CLI-parancsfájl-feladatában is használható az **Azure-folyamatokban.**

Miután telepítette a csomagot, a következő paranccsal adja ki az Azure Resource Manager-sablonokat. A **scriptPath** argumentum a projekt **asaql** fájljának abszolút elérési útja. Győződjön meg arról, hogy az asaproj.json és a JobConfig.json fájlok ugyanabban a mappában vannak a parancsfájllal. Ha a **outputPath** nincs megadva, a sablonok a **Telepítés** mappába kerülnek a projekt **tárolómappája** alatt.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Példa (macOS rendszeren)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Amikor egy Stream Analytics Visual Studio Code projekt sikeresen épül fel, a következő két Azure Resource Manager-sablonfájlt hozza létre a **bin/[Debug/Retail]/Deploy** mappában: 

*  Erőforrás-kezelő sablonfájlja

       [ProjectName].JobTemplate.json 

*  Erőforrás-kezelő paraméterfájlja

       [ProjectName].JobTemplate.parameters.json   

A parameters.json fájl alapértelmezett paraméterei a Visual Studio Code projekt beállításaiból származnak. Ha egy másik környezetben szeretne telepíteni, cserélje le a paramétereket ennek megfelelően.

> [!NOTE]
> Az összes hitelesítő adat esetében az alapértelmezett értékek null értékűek. Az értékeket a felhőbe való üzembe helyezés előtt **be kell** állítania.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Üzembe helyezés az Azure Pipelines használatával

Ez a szakasz ismerteti, hogyan hozhat létre Azure-folyamatok [létrehozása](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) és [feloldása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) folyamatok npm használatával.

Nyisson meg egy webböngészőt, és keresse meg az Azure Stream Analytics Visual Studio Code projektet.

1. A bal oldali navigációs menü **Folyamatok csoportban** válassza a **Builds (Builds)** lehetőséget. Ezután válassza az **Új folyamat lehetőséget**

   ![Új Azure-folyamat létrehozása](./media/setup-cicd-vs-code/new-pipeline.png)

2. Válassza **a Klasszikus szerkesztő használata** yaml nélküli folyamat létrehozásához lehetőséget.

3. Válassza ki a forrástípusát, a csoportprojektet és a tárházat. Ezután válassza a **Folytatás** elemet.

   ![Válassza az Azure Stream Analytics projektet](./media/setup-cicd-vs-code/select-repo.png)

4. A **Sablon kiválasztása** lapon válassza az **Üres feladat lehetőséget.**

### <a name="add-npm-task"></a>Npm-feladat hozzáadása

1. A **Feladatok** lapon válassza ki az **1.** Írja be az "npm" szót a feladatkeresésbe, és válassza az **npm lehetőséget.**

   ![Npm feladat kijelölése](./media/setup-cicd-vs-code/search-npm.png)

2. Adja meg a feladatnak **a Megjelenítendő nevet**. Módosítsa a **Parancs** beállítást *egyénire,* és írja be a következő parancsot a **Parancs és argumentumok mezőbe.** Hagyja meg a többi alapértelmezett beállítást.

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![Npm feladat konfigurációinak megadása](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>Parancssori feladat hozzáadása

1. A **Feladatok** lapon válassza ki az **1.** Keressen a **parancssorra.**

2. Adjon a feladatnak **egy Megjelenítendő nevet,** és írja be a következő parancsfájlt. Módosítsa a parancsfájlt a tárház nevével és a projekt nevével.

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![Parancssori feladat konfigurációinak megadása](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>Fájlok másolásának hozzáadása feladat

1. A **Feladatok** lapon válassza ki az **1.** Fájlok **másolása**keresése . Ezután adja meg a következő konfigurációkat.

   |Paraméter|Input (Bemenet)|
   |-|-|
   |Megjelenített név|Fájlok másolása a következőbe: $(build.artifactstagingdirectory)|
   |Forrásmappa|`$(system.defaultworkingdirectory)`| 
   |Tartalom| `**\Deploy\**` |
   |Célmappa| `$(build.artifactstagingdirectory)`|

   ![A másolási feladat konfigurációinak megadása](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>Közzétételi buildösszetevők hozzáadása feladat

1. A **Feladatok** lapon válassza ki az **1.** Keresse meg a **Build-összetevők közzététele elemet,** és válassza a beállítást a fekete nyíl ikonnal. 

2. Ne módosítsa az alapértelmezett konfigurációkat.

### <a name="save-and-run"></a>Mentés és futtatás

Miután befejezte az npm, a parancssori adatok hozzáadását, a fájlok másolását és a buildösszetevők közzétételét, válassza **a & várólista mentése**lehetőséget. Amikor a program kéri, adjon meg egy mentési megjegyzést, és válassza a **Mentés és futtatás**lehetőséget.

## <a name="release-with-azure-pipelines"></a>Kiadás az Azure-folyamatokkal

Nyisson meg egy webböngészőt, és keresse meg az Azure Stream Analytics Visual Studio Code projektet.

1. A bal oldali navigációs menü **Folyamatok csoportban** válassza a **Felengedések**lehetőséget. Ezután válassza **az Új folyamat lehetőséget**.

2. Válassza **a Kezdés üres feladattal**lehetőséget.

3. Az **Eltérések** párbeszédpanelen válassza a **+ Műtermék hozzáadása**lehetőséget. A **Forrás csoportban**válassza ki az imént létrehozott buildfolyamatot, és válassza **a Hozzáadás**lehetőséget.

   ![Build-folyamat műtermék ének megadása](./media/setup-cicd-vs-code/build-artifact.png)

4. Módosítsa az **1.** **Deploy job to test environment**

5. Adjon hozzá egy új szakaszt, és nevezze **el a feladat üzembe helyezése éles környezetbe.**

### <a name="add-tasks"></a>Tevékenységek hozzáadása

1. A feladatok legördülő menüben válassza a Telepítési feladat a **környezet teszteléséhez**lehetőséget. 

2. Válassza **+** ki az **Ügynök feladat** melletti elemet, és keresse meg az *Azure erőforráscsoport üzembe helyezését.* Adja meg a következő paramétereket:

   |Beállítás|Érték|
   |-|-|
   |Megjelenített név| *A myASAJob telepítése*|
   |Azure-előfizetés| Válassza ki az előfizetését.|
   |Műveletek| *Erőforráscsoport létrehozása vagy frissítése*|
   |Erőforráscsoport| Válasszon nevet a streamanalytics-feladatot tartalmazó teszterőforrás-csoportnak.|
   |Hely|Adja meg a teszterőforrás-csoport helyét.|
   |Sablon helye| *Csatolt műtermék*|
   |Sablon| $(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.json |
   |Sablon paraméterei|($(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.parameters.json|
   |Sablon paramétereinek felülbírálása|- Input_IoTHub1_iotHubNamespace $(test_eventhubname)|
   |Telepítési mód|Növekvő|

3. A feladatok legördülő menüben válassza **a Feladat üzembe helyezése éles környezetbe**lehetőséget.

4. Válassza **+** ki az **Ügynök feladat** melletti elemet, és keresse meg az *Azure erőforráscsoport üzembe helyezését.* Adja meg a következő paramétereket:

   |Beállítás|Érték|
   |-|-|
   |Megjelenített név| *A myASAJob telepítése*|
   |Azure-előfizetés| Válassza ki az előfizetését.|
   |Műveletek| *Erőforráscsoport létrehozása vagy frissítése*|
   |Erőforráscsoport| Válasszon nevet a Stream Analytics-feladatot tartalmazó termelési erőforráscsoportnak.|
   |Hely|Válassza ki a termelési erőforráscsoport helyét.|
   |Sablon helye| *Csatolt műtermék*|
   |Sablon| $(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.json |
   |Sablon paraméterei|($(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.parameters.json|
   |Sablon paramétereinek felülbírálása|-Input_IoTHub1_iotHubNamespace $(eventhubname)|
   |Telepítési mód|Növekvő|

### <a name="create-release"></a>Kiadás létrehozása

Kiadás létrehozásához válassza a **Kiadás létrehozása lehetőséget** a jobb felső sarokban.

![Kiadás létrehozása az Azure-folyamatok használatával](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>További források

Az Azure Data Lake Store Gen1 felügyelt identitásának kimeneti fogadóként való használatához az Azure-ba való üzembe helyezés előtt hozzáférést kell biztosítania az egyszerű szolgáltatás hoz a PowerShell használatával. További információ az [ADLS Gen1 felügyelt identitással történő központi telepítéséről az Erőforrás-kezelő sablonnal.](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)


## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Hozzon létre egy Azure Stream Analytics-felhőfeladatot a Visual Studio-kódban (előzetes verzió)](quick-create-vs-code.md)
* [A Test Stream Analytics helyi lekérdezései a Visual Studio-kóddal (előzetes verzió)](visual-studio-code-local-run.md)
* [Az Azure Stream Analytics felfedezése a Visual Studio-kóddal (előzetes verzió)](visual-studio-code-explore-jobs.md)
