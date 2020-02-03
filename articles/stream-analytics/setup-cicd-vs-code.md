---
title: Azure Stream Analytics-feladatok üzembe helyezése CI/CD NPM csomag használatával
description: Ez a cikk azt ismerteti, hogyan használható a Azure Stream Analytics CI/CD NPM-csomag a folyamatos integrációs és üzembe helyezési folyamat beállításához.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: deb6c2439cc84f196b7f42fd9f49d3ebfd057cbb
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76962191"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>Azure Stream Analytics-feladatok üzembe helyezése CI/CD NPM csomag használatával 

A Azure Stream Analytics CI/CD NPM csomaggal beállíthatja a Stream Analytics feladatokhoz szükséges folyamatos integrációt és üzembe helyezési folyamatot. Ez a cikk azt ismerteti, hogyan használható a NPM-csomag általános használata bármely CI/CD-rendszerrel, valamint az Azure-folyamatokkal való üzembe helyezésre vonatkozó konkrét utasítások.

További információ a PowerShell-lel történő telepítéséről: [üzembe helyezés Resource Manager-sablonfájl és Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Azt is megtudhatja, hogyan [használhatja az objektumokat paraméterként egy Resource Manager-sablonban](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

## <a name="build-the-vs-code-project"></a>A VS Code projekt összeállítása

Az **ASA-streamanalytics-vel NPM-** csomag használatával engedélyezheti a folyamatos integrációt és üzembe helyezést Azure stream Analytics feladatokhoz. A NPM csomag lehetővé teszi az eszközök számára, hogy [stream Analytics Visual Studio Code-projektekhez](quick-create-vs-code.md)Azure Resource Manager sablonokat lehessen előállítani. A Visual Studio Code telepítése nélkül is használható Windows, macOS és Linux rendszereken.

[A csomagot közvetlenül letöltheti](https://www.npmjs.com/package/azure-streamanalytics-cicd) , vagy [globálisan](https://docs.npmjs.com/downloading-and-installing-packages-globally) telepítheti a `npm install -g azure-streamanalytics-cicd` parancs használatával. Ez az ajánlott módszer, amely egy **Azure-folyamatokban**lévő build-folyamat PowerShell-vagy Azure CLI-parancsfájl-feladatában is használható.

A csomag telepítése után a következő paranccsal exportálhatja a Azure Resource Manager sablonokat. A **scriptPath** argumentum a **asaql** fájl abszolút elérési útja a projektben. Győződjön meg arról, hogy a asaproj. JSON és a JobConfig. JSON fájlok ugyanabban a mappában találhatók, mint a parancsfájl. Ha a **outputPath** nincs megadva, a sablonok a projekt **bin** mappájában, a **központi telepítés** mappában lesznek elhelyezve.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Példa (macOS rendszeren)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Stream Analytics Visual Studio Code-projekt sikeres létrehozásakor a a következő két Azure Resource Manager sablonfájlt hozza létre a **bin/[debug/Retail]/Deploy** mappában: 

*  Resource Manager-sablonfájl

       [ProjectName].JobTemplate.json 

*  Resource Manager-paraméterek fájlja

       [ProjectName].JobTemplate.parameters.json   

A Parameters. JSON fájlban lévő alapértelmezett paraméterek a Visual Studio Code projekt beállításaiból származnak. Ha egy másik környezetbe szeretne telepíteni, cserélje le a paramétereket ennek megfelelően.

> [!NOTE]
> Az összes hitelesítő adat esetében az alapértelmezett értékek NULL értékre vannak állítva. A felhőbe való üzembe helyezés előtt **be kell állítania** az értékeket.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Üzembe helyezés Azure-folyamatokkal

Ez a szakasz részletesen [ismerteti, hogyan](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) hozhat létre és [szabadíthat fel](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) folyamatokat a NPM használatával az Azure-folyamatok létrehozásával.

Nyisson meg egy webböngészőt, és navigáljon a Azure Stream Analytics Visual Studio Code projekthez.

1. A bal oldali navigációs menü **folyamatok** területén válassza a **buildek**lehetőséget. Ezután válassza az **új folyamat** elemet.

   ![Új Azure-folyamat létrehozása](./media/setup-cicd-vs-code/new-pipeline.png)

2. Válassza **a klasszikus szerkesztő használata** lehetőséget a YAML nélküli folyamat létrehozásához.

3. Válassza ki a forrás típusát, a csapat projektjét és a tárházat. Ezután válassza a **Folytatás** elemet.

   ![Azure Stream Analytics projekt kiválasztása](./media/setup-cicd-vs-code/select-repo.png)

4. A **sablon választása** lapon válassza az **üres feladatot**.

### <a name="add-npm-task"></a>NPM feladat hozzáadása

1. A **feladatok** lapon válassza ki a plusz jelet a **Agent 1. feladat**mellett. Adja meg a "NPM" kifejezést a feladat keresése területen, majd válassza a **NPM**lehetőséget.

   ![NPM feladat kiválasztása](./media/setup-cicd-vs-code/search-npm.png)

2. Adja meg a feladat **megjelenítendő nevét**. Módosítsa a **parancsot** *egyénire* , és adja meg a következő parancsot a **parancsban és argumentumokban**. Hagyja meg a többi alapértelmezett beállítást.

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![Adja meg a NPM feladat konfigurációit](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>Parancssori feladat hozzáadása

1. A **feladatok** lapon válassza ki a plusz jelet a **Agent 1. feladat**mellett. Keresse meg a **parancssort**.

2. Adja meg a feladat **megjelenítendő nevét** , és adja meg a következő parancsfájlt. Módosítsa a szkriptet az adattár nevével és a projekt nevével.

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![Adja meg a parancssori feladathoz tartozó konfigurációkat](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>Fájlok másolása feladat hozzáadása

1. A **feladatok** lapon válassza ki a plusz jelet a **Agent 1. feladat**mellett. **Fájlok másolásának**keresése. Ezután adja meg a következő konfigurációkat.

   |Paraméter|Input (Bemenet)|
   |-|-|
   |Megjelenített név|Fájlok másolása ide: $ (Build. artifactstagingdirectory)|
   |Forrás mappája|`$(system.defaultworkingdirectory)`| 
   |Tartalom| `**\Deploy\**` |
   |Célmappa| `$(build.artifactstagingdirectory)`|

   ![A másolási feladathoz tartozó konfigurációk megadása](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>Közzététel-összeállítási összetevők hozzáadása feladat

1. A **feladatok** lapon válassza ki a plusz jelet a **Agent 1. feladat**mellett. Keressen rá a **közzétett Build** -összetevők keresése elemre, és válassza a fekete nyíl ikont. 

2. Ne módosítsa az alapértelmezett konfigurációk egyikét sem.

### <a name="save-and-run"></a>Mentés és Futtatás

Miután végzett a NPM, a parancssorból, a fájlok másolásával és a Build-összetevők közzétételével kapcsolatos feladatok hozzáadásával, válassza a **mentés & üzenetsor**lehetőséget. Amikor a rendszer kéri, adjon meg egy mentési megjegyzést, és válassza a **Mentés és Futtatás**lehetőséget.

## <a name="release-with-azure-pipelines"></a>Kiadás Azure-folyamatokkal

Nyisson meg egy webböngészőt, és navigáljon a Azure Stream Analytics Visual Studio Code projekthez.

1. A bal oldali navigációs menü **folyamatok** területén válassza a **kiadások**elemet. Ezután válassza az **új folyamat**lehetőséget.

2. Válassza **az indítás üres feladatokkal**lehetőséget.

3. Az összetevők **mezőben válassza az** **+ összetevő hozzáadása**elemet. A **forrás**területen válassza ki az imént létrehozott Build folyamatot, majd válassza a **Hozzáadás**lehetőséget.

   ![Adja meg a folyamat létrehozása összetevőt](./media/setup-cicd-vs-code/build-artifact.png)

4. Módosítsa az 1. **fázis** nevét a **feladatok tesztelési környezetbe való telepítéséhez**.

5. Vegyen fel egy új szakaszt, és nevezze el az **üzembe helyezési feladatot éles környezetbe**.

### <a name="add-tasks"></a>Feladatok hozzáadása

1. A feladatok legördülő menüben válassza a **feladat üzembe helyezése a tesztkörnyezet teszteléséhez**lehetőséget. 

2. Válassza ki az **ügynök feladata** melletti **+** , és keresse meg az *Azure-erőforráscsoport üzembe helyezését*. Adja meg a következő paramétereket:

   |Beállítás|Value (Díj)|
   |-|-|
   |Megjelenített név| *MyASAJob üzembe helyezése*|
   |Azure-előfizetés| Válassza ki az előfizetését.|
   |Műveletek| *Erőforráscsoport létrehozása vagy frissítése*|
   |Erőforráscsoport| Válassza ki a Stream Analytics feladatot tartalmazó tesztelési erőforráscsoport nevét.|
   |Földrajzi egység|Válassza ki a tesztelési erőforráscsoport helyét.|
   |Sablon helye| *Társított összetevő*|
   |Sablon| $ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.json |
   |Sablon paraméterei|($ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.parameters.json|
   |Sablon paramétereinek felülbírálása|-Input_IoTHub1_iotHubNamespace $ (test_eventhubname)|
   |Üzembe helyezési mód|Növekményes|

3. A feladatok legördülő menüben válassza a **feladat üzembe helyezése éles környezetben**lehetőséget.

4. Válassza ki az **ügynök feladata** melletti **+** , és keresse meg az *Azure-erőforráscsoport üzembe helyezését*. Adja meg a következő paramétereket:

   |Beállítás|Value (Díj)|
   |-|-|
   |Megjelenített név| *MyASAJob üzembe helyezése*|
   |Azure-előfizetés| Válassza ki az előfizetését.|
   |Műveletek| *Erőforráscsoport létrehozása vagy frissítése*|
   |Erőforráscsoport| Válassza ki az üzemi erőforráscsoport nevét, amely a Stream Analytics feladatot fogja tartalmazni.|
   |Földrajzi egység|Válassza ki az üzemi erőforráscsoport helyét.|
   |Sablon helye| *Társított összetevő*|
   |Sablon| $ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.json |
   |Sablon paraméterei|($ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.parameters.json|
   |Sablon paramétereinek felülbírálása|-Input_IoTHub1_iotHubNamespace $ (eventhubname)|
   |Üzembe helyezési mód|Növekményes|

### <a name="create-release"></a>Kiadás létrehozása

A kiadás létrehozásához válassza a jobb felső sarokban található **kiadás létrehozása** elemet.

![Kiadás létrehozása az Azure-folyamatokkal](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>További források

Ha Azure Data Lake Store Gen1 felügyelt identitást szeretne használni kimeneti fogadóként, az Azure-ba való üzembe helyezés előtt meg kell adnia a szolgáltatásnevet a PowerShell használatával. További információ a [ADLS Gen1 felügyelt identitással való üzembe helyezéséhez Resource Manager-sablonnal](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="next-steps"></a>Következő lépések

* [Gyors útmutató: Azure Stream Analytics Cloud-feladatok létrehozása a Visual Studio Code-ban (előzetes verzió)](quick-create-vs-code.md)
* [Stream Analytics lekérdezések tesztelése helyileg a Visual Studio Code-ban (előzetes verzió)](visual-studio-code-local-run.md)
* [Ismerkedés a Azure Stream Analytics a Visual Studio Code (előzetes verzió) szolgáltatással](visual-studio-code-explore-jobs.md)
