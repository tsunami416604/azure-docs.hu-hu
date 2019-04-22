---
title: Az Azure-folyamatok használatával létrehozása és üzembe helyezése a HPC-megoldások – Azure Batch |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe egy HPC-alkalmazás futtatása Azure Batch a build és kiadás folyamat.
author: christianreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.openlocfilehash: 5b7c44d3ea3394ff728adfb9d9fd72293138fb2e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59494644"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Azure folyamatok használatával hozhat létre és HPC-megoldások üzembe helyezése

Az Azure DevOps-szolgáltatásokkal, egy egyéni application készítése során, a fejlesztői csapatok által használt eszközök széles adja meg. Az Azure DevOps által biztosított eszközök is jelenti azt, hogy automatikus létrehozását és tesztelését, nagy teljesítményű számítási megoldásokat. Ez a cikk bemutatja, hogyan állíthat be egy folyamatos integrációs (CI) és folyamatos üzembe helyezés (CD) használatával egy nagy teljesítményű Azure folyamatok számítási üzembe helyezett Azure Batch megoldás.

A folyamatok az Azure számos modern CI/CD-folyamatok létrehozását, üzembe helyezéséhez, tesztelés és felügyeleti szoftverek. Ezek a folyamatok felgyorsítása a szoftverterjesztés, amely lehetővé teszi, hogy a kód összpontosíthat, ahelyett hogy infrastruktúrájának és műveleteinek támogatásához.

## <a name="create-an-azure-pipeline"></a>Hozzon létre egy Azure-folyamat

Ebben a példában a rendszer létrehoz egy build és kibocsátásában üzembe helyezése az Azure Batch-infrastruktúrát, és a egy alkalmazáscsomagot kiadási. Feltételezve, hogy a kód létrehozása helyileg, ez az az általános üzembe helyezési folyamat:

![A folyamat üzembe helyezési folyamatot bemutató ábra.](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Beállítás

A jelen cikkben ismertetett lépések követéséhez szüksége van egy Azure DevOps-szervezet és a egy csoportprojektet.

* [Hozzon létre egy Azure DevOps-szervezet](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Az Azure DevOps-projekt létrehozása](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>A környezet verziókövetését

Verziókövetés lehetővé teszi a csapatok nyomon követheti a kódbázisnak végzett módosításokat, és vizsgálja meg a kód korábbi verziói.

Általában a verziókövetés, az aktuális közötti közvetlen szoftver kóddal úgy Gondoltuk. Mi a helyzet az alapul szolgáló infrastruktúra? Elértünk infrastruktúra kódként, ahol az Azure Resource Manager-sablonok vagy más nyílt forráskódú lehetőségeket a használjuk az alapul szolgáló infrastruktúra deklaratív definiálása.

Ez a minta az erősen támaszkodik egy Resource Manager-sablonokat (JSON-dokumentumok) és a meglévő bináris fájlok száma. Ezekben a példákban másolhatja az adattárba, és azok leküldése az Azure DevOps.

A jelen példában használt kódbázis struktúráját a következőhöz;

* Egy **arm-sablonok** mappába, az Azure Resource Manager-sablonok számú. A sablonok Ez a cikk ismerteti.
* A **ügyfélalkalmazás** mappát, amely egy másolatot, a [Azure Batch .NET fájl feldolgozása ffmpeg segítségével](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) minta. Ebben a cikkben nem erre van szükség.
* Egy **hpc-alkalmazás** mappát, amely a Windows 64 bites verzióját [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).
* A **folyamatok** mappát. A létrehozási folyamat sbalování egy YAML-fájlt tartalmazza. Ez a cikk a következő cikkben.

Ez a szakasz azt feltételezi, hogy ismeri a verzió-vezérlés és tervezési Resource Manager-sablonok. Ha nem ismeri ezeket a fogalmakat, a következő oldalakon további információt.

* [Mi az, hogy a verziókövetés?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Az Azure Resource Manager-sablonok struktúrája és szintaxisa](../azure-resource-manager/resource-group-authoring-templates.md)

#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Ebben a példában több Resource Manager-sablonok üzembe helyezni a megoldást használja. Ehhez számos képesség sablonok (egységek és a modulok hasonló) egy adott adatrészletet funkciókat megvalósító használjuk. Azt is használhatja egy teljes körű megoldás sablont, amely felelős képességek mögöttes együtt történő visszaállítását. Van néhány előnye ennek a megoldásnak:

* Az alapul szolgáló funkció sablonokat külön-külön teszteltük egység is lehet.
* Az alapul szolgáló funkció sablonok határozható meg, a szervezeten belül egy szabványos, és több megoldás újra felhasználhatók.

Ebben a példában van egy teljes körű megoldás sablon (deployment.json), amely három sablonnal üzembe helyezi. Az alapul szolgáló sablonok olyan képességet sablonokat, a megoldás egy adott aspektusa telepítéséért felelős.

![A csatolt sablon struktúra Azure Resource Manager-sablonok használatával – példa](media/batch-ci-cd/ARMTemplateHierarchy.png)

Az első sablon áttekintjük, hogy van egy Azure Storage-fiók. A megoldás egy tárfiókot, a Batch-fiókhoz az alkalmazás telepítéséhez szükséges. Észrevennék, érdemes a [Resource Manager sablon referencia-útmutató a Microsoft.Storage erőforrás-típus](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) Storage-fiókok Resource Manager-sablonok készítése során.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

Ezt követően áttekintjük az Azure Batch-fiók sablont. Az Azure Batch-fiók készletek (gépek csoportosításán) között számos olyan alkalmazások futtatására platformként működik. Észrevennék, érdemes a [Resource Manager sablon referencia-útmutató Microsoft.Batch erőforrástípusok](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) Resource Manager-sablonok létrehozását, a Batch-fiókokkal.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

A következő sablon létrehozása az Azure Batch-készletet (a háttérrendszer gépeken az alkalmazások feldolgozásához) mutat be. Észrevennék, érdemes a [Resource Manager sablon referencia-útmutató Microsoft.Batch erőforrástípusok](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) Batch-fiók készletek Resource Manager-sablonok készítésekor.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

Végül van egy sablont, amely az orchestrator alkalmazáshoz hasonlóan működik. Ez a sablon üzembe helyezése a képesség sablonok felelős.

Akkor is is tudjon meg többet [társított Azure Resource Manager-sablonok létrehozására](../azure-resource-manager/resource-manager-tutorial-create-linked-templates.md) egy külön cikkben.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

#### <a name="the-hpc-solution"></a>A HPC-megoldás

Az infrastruktúra és szoftverek kódként definiálhatók és védelmicsoport-készletek ugyanabban az adattárban.

Ebben a megoldásban az ffmpeg segítségével az alkalmazáscsomag része lesz. Az ffmpeg csomag letölthető [Itt](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Példa Git-tárház szerkezete](media/batch-ci-cd/git-repository.jpg)

Van a tárházhoz négy fő részből áll:

* A **arm-sablonok** mappát, amely tárolja az infrastruktúra mint kód
* A **hpc-alkalmazás** ffmpeg bináris fájljait tartalmazó mappát
* A **folyamatok** buildelési folyamat definícióját tartalmazó mappát.
* **Nem kötelező**: A **ügyfélalkalmazás** mappát, amely a .NET-alkalmazás kódját szeretné tárolni. Nem használja ezt a mintát, de a saját project Kezdésként keresztül egy ügyfélalkalmazás a HPC Batch-alkalmazás futtatásának végrehajtásához.

> [!NOTE]
> Ez a csupán egy példa a struktúrát a kódbázisnak az. Ez a megközelítés annak bemutatására, hogy alkalmazást, az infrastruktúra és a folyamat kód tárolása ugyanabban az adattárban a célokat szolgál.

Most, hogy be van állítva a forráskódot, megkezdhetjük az első hozhat létre.

## <a name="continuous-integration"></a>Folyamatos integráció

[Az Azure folyamatok](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops), Azure DevOps-szolgáltatásokban, segítséget nyújt az alkalmazások számára a buildelési, tesztelési és üzembe helyezési folyamat implementálásához.

Ebben a szakaszban a folyamat általában teszteket kódot, és hozhat létre a megfelelő kódrészletek, a szoftver. Számát és típusát tesztek, és minden további feladatot, amely futtatja a szélesebb körű build attól függ, és kiadási stratégiát.

## <a name="preparing-the-hpc-application"></a>A HPC-alkalmazás előkészítése

Ebben a példában fogunk dolgozni a **hpc-alkalmazás** mappát. A **hpc-alkalmazás** mappa a következő az ffmpeg szoftver, amely az Azure Batch-fiókon belül elindul.

1. Keresse meg az Azure folyamatok buildek szakaszában az Azure DevOps-szervezetben. Hozzon létre egy **új adatcsatorna**.

    ![Új Buildelési folyamat létrehozása](media/batch-ci-cd/new-build-pipeline.jpg)

1. A Build-folyamatok létrehozására két lehetősége van:

    a. [A vizuális Tervezőeszköz segítségével](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Ennek használatához kattintson "A vizuális tervező használata a" az a **új adatcsatorna** lapot.

    b. [YAML használatával összeállít](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). Egy új YAML-folyamatot az Azure-Adattárakkal vagy GitHub lehetőséget az új folyamat oldalon kattintva hozhatja létre. Azt is megteheti tárolása az alábbi példában a verziókövetési rendszerben, és kattintson a vizuális Tervező, és ezután a YAML sablonnal hivatkozni egy meglévő YAML-fájlt.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. Ha a build igény szerint van konfigurálva, válassza **várólistára & mentése**. Ha engedélyezve van a folyamatos integráció (az a **eseményindítók** szakaszban), a build automatikusan aktiválódik egy új véglegesítés az adattárban szeretné tenni, a build a feltételeknek megfelelő beállítása.

    ![Egy meglévő Build-folyamatot – példa](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Az Azure DevOps-beli build állapotát az élő frissítés megtekintése a **hozhat létre** Azure folyamatok szakaszában. Válassza ki a megfelelő build a builddefiníció.

    ![A build élő kimeneteinek megtekintése](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Ha egy ügyfélalkalmazás segítségével hajtsa végre az HPC Batch-alkalmazás, az adott alkalmazáshoz külön builddefiníció létrehozása szeretné. Útmutatók a számos annak a [Azure folyamatok](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) dokumentációját.

## <a name="continuous-deployment"></a>Folyamatos üzembe helyezés

Az Azure folyamatok is telepítheti az alkalmazást, és az alapul szolgáló infrastruktúra. [Folyamatok felszabadítása](https://docs.microsoft.com/azure/devops/pipelines/release/what-is-release-management?view=azure-devops) az a komponens, amely lehetővé teszi a folyamatos üzembe helyezést, és a kibocsátási folyamat automatizálható.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Az alkalmazás üzembe helyezése és az alapul szolgáló infrastruktúra

Nincsenek érintett infrastruktúra telepítése több lépésből áll. Rendelkezik használtuk, [kapcsolódó sablonok](../azure-resource-manager/resource-group-linked-templates.md), akkor ezeket a sablonokat kell lennie egy nyilvános végpontot (HTTP vagy HTTPS) elérhető. Ez lehet egy tárházban a Githubon, vagy egy Azure Blob Storage-fiókot, vagy egy másik tárolási helye. A feltöltött sablon összetevők maradhat, biztonságos, mint egy privát módban tárolhatók, de valamilyen közös hozzáférésű jogosultságkód (SAS) használatával elért. A következő példa bemutatja, hogyan helyezhet üzembe a sablonokat az Azure Storage-blobból az infrastruktúrát.

1. Hozzon létre egy **új kiadási definíció**, és válassza ki egy üres definíciót. Ezután meg kell egy, a folyamat az újonnan létrehozott környezet átnevezése.

    ![Kezdeti kiadás folyamat](media/batch-ci-cd/Release-0.jpg)

1. Függőség létrehozása a HPC-alkalmazás eredményének hozhat létre folyamathoz.

    > [!NOTE]
    > Ismét, vegye figyelembe a **forrás Alias**, ahogy feladatokat a kiadási definíció belül létrehozásakor ez lesz szükség.

    ![A megfelelő buildelési folyamat-összetevő hivatkozás a HPCApplicationPackage létrehozásához](media/batch-ci-cd/Release-1.jpg)

1. Egy hivatkozás egy másik összetevő, ennek során egy Azure-tárház létrehozásához. Ez a Resource Manager-sablonok a tárházban tárolt eléréséhez szükséges. Resource Manager-sablonok összeállításához nincs szükség, mert nem kell a buildelési folyamat keresztül küldje le őket.

    > [!NOTE]
    > Ismét, vegye figyelembe a **forrás Alias**, ahogy feladatokat a kiadási definíció belül létrehozásakor ez lesz szükség.

    ![Az Azure-Adattárakkal egy összetevő hivatkozás létrehozásához](media/batch-ci-cd/Release-2.jpg)

1. Keresse meg a **változók** szakaszban. Azt javasoljuk, hogy hozzon létre egy változók száma a folyamatban, nem bevitelével ugyanazokat az adatokat több feladatokra. Az ebben a példában, és hogy azok hatással az üzembe helyezés használt változók az alábbiak.

    * **applicationStorageAccountName**: HPC-alkalmazás bináris fájljainak tárolásához a Tárfiók neve
    * **batchAccountApplicationName**: Az Azure Batch-fiókban az alkalmazás neve
    * **batchAccountName**: Az Azure Batch-fiók neve
    * **batchAccountPoolName**: Virtuális gépek a feldolgozást végző-készlet neve
    * **batchApplicationId**: Az Azure Batch-alkalmazás egyedi azonosítója
    * **batchApplicationVersion**: A batch-alkalmazás (azaz az ffmpeg bináris fájlok) sémantická verze
    * **Hely**: Az Azure-erőforrások központi helye
    * **resourceGroupName**: Létre kell hozni, az erőforráscsoport nevét, és amelyen telepítve lesz az erőforrások
    * **storageAccountName**: A csatolt Resource Manager-sablonok tárolásához a Tárfiók neve

    ![A változók beállítása az Azure-folyamatok kiadás – példa](media/batch-ci-cd/Release-4.jpg)

1. Keresse meg a fejlesztői környezetben a feladatokat. Az az alábbi, láthatja a hat tevékenység. Ezek a feladatok lesz: Töltse le az ffmpeg zip-fájlok, üzembe helyezhet egy tárfiókot, a beágyazott Resource Manager-sablonok üzemeltetésére, másolja ezeket a Resource Manager-sablonok a tárfiókba, a batch-fiók és a szükséges függőségek telepítése, az alkalmazás létrehozása az Azure Batch-fiók és feltöltése az alkalmazáscsomagot, az Azure Batch-fiókhoz.

    ![A feladatokat a HPC-alkalmazás az Azure Batch feloldásához használt – példa](media/batch-ci-cd/Release-3.jpg)

1. Adja hozzá a **letöltési folyamat összetevő (előzetes verzió)** feladatot, és állítsa be a következő tulajdonságokat:
    * **Megjelenített név:** ApplicationPackage ügynök letöltése
    * **Töltse le a lehívandó összetevő neve:** hpc-alkalmazás
    * **Letölti az elérési út**: $(System.DefaultWorkingDirectory)

1. Hozzon létre egy Tárfiókot, az összetevők tárolására. Egy meglévő tárfiókot a megoldásból használható, de az önálló minta és a tartalom elkülönítési igyekszünk egy dedikált tárfiókot az összetevők (pontosabban a Resource Manager-sablonok).

    Adja hozzá a **Azure erőforráscsoport-telepítés** feladatot, és állítsa be a következő tulajdonságokat:
    * **Megjelenített név:** Storage-fiók üzembe helyezése Resource Manager-sablonok
    * **Azure-előfizetés:** Válassza ki a megfelelő Azure-előfizetést
    * **A művelet**: Erőforráscsoport létrehozása vagy frissítése
    * **Erőforráscsoport**: $(resourceGroupName)
    * **Hely**: $(location)
    * **Template**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    * **Bírálja felül a Sablonparaméterek**: - accountName $(storageAccountName)

1. A verziókövetési rendszerekből az összetevők feltöltése a Storage-fiókban. Van egy Azure-folyamat tevékenységet végezni. Ez a feladat részeként a tárolási fiók tároló URL-címe és a SAS-Token is lehet használt kimeneti adattípus Azure folyamatokban változóhoz. Ez azt jelenti, hogy az ügynök fázis során felhasználhatók.

    Adja hozzá a **Azure fájlmásolás** feladatot, és állítsa be a következő tulajdonságokat:
    * **Source:** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/
    * **Az Azure kapcsolattípus**: Azure Resource Manager
    * **Azure-előfizetés:** Válassza ki a megfelelő Azure-előfizetést
    * **Cél típusa**: Azure-blob
    * **Erőforrás-kezelő Tárfiók**: $(storageAccountName)
    * **Tárolónév**: sablonok
    * **Storage-tároló URI**: templateContainerUri
    * **Tárolási tároló SAS-Token**: templateContainerSasToken

1. Az orchestrator-sablon üzembe helyezése. Az orchestrator-sablont a korábban visszahívása, megfigyelheti, hogy voltak-e a tárolási fiók tároló URL-címe, mellett az SAS-token paraméterek. Észrevehető, hogy a változók megadása kötelező a Resource Manager-sablonban vagy tartják a változók szakaszban, a kiadási definíció, vagy egy másik Azure-folyamatok feladat (például az Azure Blob másolási feladat része) a beállított.

    Adja hozzá a **Azure erőforráscsoport-telepítés** feladatot, és állítsa be a következő tulajdonságokat:
    * **Megjelenített név:** Az Azure Batch üzembe helyezése
    * **Azure-előfizetés:** Válassza ki a megfelelő Azure-előfizetést
    * **A művelet**: Erőforráscsoport létrehozása vagy frissítése
    * **Erőforráscsoport**: $(resourceGroupName)
    * **Hely**: $(location)
    * **Template**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    * **Bírálja felül a Sablonparaméterek**: ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Általános gyakorlat, hogy az Azure Key Vault-feladatokat. Ha az egyszerű szolgáltatás (kapcsolat az Azure-előfizetéshez) rendelkezik egy megfelelő hozzáférési szabályzatok beállítása, titkos kódok tölthet le az Azure Key Vault, és változókat a folyamatban használhatók. A titkos kód nevét és a kapcsolódó érték fog szerepelni. Például sshPassword a titkos kulcs és a kiadási definíció a $(sshPassword) sikerült lehet hivatkozni.

1. A következő lépéseket az Azure CLI hívja meg. Az első alkalmazás létrehozása az Azure Batchben szolgál. és töltse fel a hozzá tartozó csomagok.

    Adja hozzá a **Azure CLI-vel** feladatot, és állítsa be a következő tulajdonságokat:
    * **Megjelenített név:** Alkalmazás létrehozása az Azure Batch-fiók
    * **Azure-előfizetés:** Válassza ki a megfelelő Azure-előfizetést
    * **Parancsfájl helye**: Beágyazott parancsfájlja
    * **Beágyazott parancsfájlja**: ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. A második lépés az alkalmazáshoz társított csomagok feltöltésére szolgál. A mi esetünkben az ffmpeg-fájlokat.

    Adja hozzá a **Azure CLI-vel** feladatot, és állítsa be a következő tulajdonságokat:
    * **Megjelenített név:** Csomag feltöltése az Azure Batch-fiók
    * **Azure-előfizetés:** Válassza ki a megfelelő Azure-előfizetést
    * **Parancsfájl helye**: Beágyazott parancsfájlja
    * **Beágyazott parancsfájlja**: ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > A verziószám az alkalmazás-csomag egy változóban van beállítva. Ez akkor hasznos, ha a csomag korábbi verzióinak felülírása működik az Ön számára, és ha manuálisan az Azure Batch leküldte a csomag verziószáma szabályozni szeretné.

1. Hozzon létre egy új kiadási kiválasztásával **kiadási > hozzon létre egy új kiadási**. Aktivált, miután a hivatkozásra az új kiadás állapotának megtekintéséhez.

1. Az ügynök élő kimenete kiválasztásával is megtekintheti a **naplók** gomb alatt a környezet.

    ![A kiadás állapotának megtekintése](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>A környezet tesztelése

A környezet beállítása után győződjön meg arról, a következő vizsgálatokat is sikeresen befejeződött.

Csatlakoztassa az új Azure Batch-fiókhoz, egy PowerShell-parancssorból az Azure CLI használatával.

* Jelentkezzen be az Azure-fiókjába `az login` és kövesse az utasításokat a hitelesítéshez.
* Ettől kezdve hitelesíthetők a Batch-fiók: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>A rendelkezésre álló alkalmazások listája

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Ellenőrizze, érvényes a készlet

```azurecli
az batch pool list
```

Jegyezze fel az értékét a `currentDedicatedNodes` Ez a parancs kimenetében. Ez az érték a következő teszt módosul.

#### <a name="resize-the-pool"></a>A készlet átméretezése

Méretezze át a készlethez, hogy nincsenek a számítási csomópontok, feladat és a feladat tesztelése érhető el, tekintse meg az aktuális állapot megtekintéséhez, amíg a méretezés befejezése és a rendelkezésre álló csomópontok a készlet lista paranccsal

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>További lépések

Ez a cikk kívül két oktatóanyagok, amelyek ténylegesen használják az ffmpeg, .NET és Python használatával. További információk oktatóanyagokhoz hogyan kommunikál a Batch-fiók használatával egy egyszerű alkalmazás jelenik meg.

* [Párhuzamos számítási feladatok futtatása az Azure Batch Python API használatával](tutorial-parallel-python.md)
* [Párhuzamos számítási feladatok futtatása az Azure Batch .NET API használatával](tutorial-parallel-dotnet.md)
