---
title: Az Azure-folyamatok használata hpc-megoldások & üzembe helyezéséhez - Azure Batch
description: Ismerje meg, hogyan telepítheti az Azure Batch-en futó HPC-alkalmazások buildelési/kiadási folyamatát.
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.service: batch
ms.openlocfilehash: 50cb711dfd16c2a8718d13ba9255ace1e7e3e26d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533130"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>HPC-megoldások létrehozása és üzembe helyezése az Azure Pipelines használatával

Az Azure DevOps-szolgáltatások számos olyan eszközt biztosítanak, amelyeket a fejlesztőcsapatok egyéni alkalmazások létrehozásakor használnak. Az Azure DevOps által biztosított eszközök lefordíthatók a nagy teljesítményű számítási megoldások automatizált létrehozásában és tesztelésében. Ez a cikk bemutatja, hogyan állíthat be folyamatos integrációs (CI) és folyamatos üzembe helyezés (CD) az Azure Pipelines használatával az Azure Batch-en telepített nagy teljesítményű számítási megoldás.

Az Azure Pipelines számos modern CI/CD-folyamatot biztosít szoftverek létrehozásához, üzembe helyezéséhez, teszteléséhez és figyeléséhez. Ezek a folyamatok felgyorsítják a szoftverkézbesítést, így az infrastruktúra és a műveletek támogatása helyett a kódra összpontosíthat.

## <a name="create-an-azure-pipeline"></a>Azure-folyamat létrehozása

Ebben a példában létrehozunk egy build- és kiadási folyamatot az Azure Batch-infrastruktúra üzembe helyezéséhez és egy alkalmazáscsomag felszabadításához. Feltételezve, hogy a kód helyileg lett kifejlesztve, ez az általános telepítési folyamat:

![A folyamat üzembe helyezésének folyamatát bemutató diagram](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Telepítés

A cikkben leírt lépések végrehajtásához egy Azure DevOps-szervezetre és egy csoportprojektre van szükség.

* [Azure DevOps-szervezet létrehozása](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Projekt létrehozása az Azure DevOps-ban](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Forrásvezérlés a környezethez

A forrásellenőrzés lehetővé teszi a csapatok számára a kódbázison végrehajtott módosítások nyomon követését és a kód korábbi verzióinak vizsgálatát.

Jellemzően, forrás irányít van gondolkodás -ból odaad-a- kéz -val- kéz -val szoftver kód. Mi a helyzet a mögöttes infrastruktúrával? Ez elvezet minket az infrastruktúra kódként, ahol az Azure Resource Manager-sablonokat vagy más nyílt forráskódú alternatívákat fogunk használni az alapul szolgáló infrastruktúra deklaratív meghatározásához.

Ez a minta nagymértékben támaszkodik számos Erőforrás-kezelő sablonok (JSON-dokumentumok) és a meglévő bináris fájlokat. Ezeket a példákat átmásolhatja a tárházba, és leküldéses őket az Azure DevOps.You can copy these examples into your repository and push them to Azure DevOps.

Az ebben a mintában használt kódbázis-struktúra a következőkre hasonlít;

* Egy **arm-templates** mappa, amely számos Azure Resource Manager-sablont tartalmaz. A sablonokat ebben a cikkben ismertetjük.
* Egy **ügyfél-alkalmazás** mappa, amely az Azure Batch .NET fájlfeldolgozás egy példányát [ffmpeg mintával.](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) Ez nem szükséges ehhez a cikkhez.
* Egy **hpc-alkalmazás** mappa, amely a Windows 64 bites változata [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).
* Egy **pipelines** folyamatrendszer-mappa. Ez egy YAML-fájlt tartalmaz, amely felvázolja a létrehozási folyamatot. Ezt tárgyalja a cikkben.

Ez a szakasz feltételezi, hogy ismeri a verziókövetést és az Erőforrás-kezelő sablonok tervezését. Ha nem ismeri ezeket a fogalmakat, további információt a következő oldalakon talál.

* [Mi a forrásellenőrzés?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Az Azure Resource Manager-sablonok struktúrája és szintaxisa](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Ez a példa több Erőforrás-kezelő sablont használ a megoldás üzembe helyezéséhez. Ehhez számos képességsablont használunk (hasonlóan az egységekhez vagy modulokhoz), amelyek egy adott funkciódarabot valósítanak meg. Teljes körű megoldássablont is használunk, amely felelős az alapul szolgáló képességek egyesítésért. Van egy pár előnye, hogy ezt a megközelítést:

* Az alapul szolgáló képességsablonok egyedileg tesztelhetők.
* Az alapul szolgáló képességsablonok egy szervezeten belüli szabványként definiálhatók, és több megoldásban is felhasználhatók.

Ebben a példában van egy végpontok között megoldássablon (deployment.json), amely három sablont telepít. Az alapul szolgáló sablonok képességsablonok, amelyek a megoldás egy adott aspektusának üzembe helyezéséért felelősek.

![Példa csatolt sablonszerkezetre az Azure Resource Manager-sablonok használatával](media/batch-ci-cd/ARMTemplateHierarchy.png)

Az első sablon, amelyet megtekintünk, egy Azure Storage-fiók. A megoldásunkhoz egy tárfiókra van szükség az alkalmazás batch-fiókra való üzembe helyezéséhez. Érdemes tisztában lenni a Resource Manager sablon referencia útmutató a [Microsoft.Storage erőforrás-típusok](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) létrehozásakor Resource Manager sablonok tárfiókok.

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

Ezután megnézzük az Azure Batch-fiók sablont. Az Azure Batch-fiók platformként működik, amely számos alkalmazást futtat a készletek (gépek csoportosítása) között. Érdemes tudni a [Microsoft.Batch erőforrástípusok Erőforrás-kezelő sablonútmutatójával,](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) amikor Erőforrás-kezelő sablonokat hoz össze a batch-fiókokhoz.

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

A következő sablon egy azure batch-készlet (az alkalmazások feldolgozásához a háttérgépek) létrehozása példát mutat be. Érdemes tudni a [Microsoft.Batch erőforrástípusok Erőforrás-kezelő sablonútmutatójával,](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) amikor Erőforrás-kezelő sablonokat hoz össze a kötegelt fiókkészletekhez.

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

Végül, van egy sablon, amely hasonló egy orchestrator. Ez a sablon felelős a képességsablonok üzembe helyezéséért.

A [csatolt Azure Resource Manager-sablonok létrehozásáról](../azure-resource-manager/templates/template-tutorial-create-linked-templates.md) egy külön cikkben is többet megtudhat.

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

#### <a name="the-hpc-solution"></a>A HPC megoldás

Az infrastruktúra és a szoftver kódként definiálható, és ugyanabban a tárházban található.

Ehhez a megoldáshoz az ffmpeg az alkalmazáscsomag. Az ffmpeg csomag [letölthető itt](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Példa Git-tárház szerkezetére](media/batch-ci-cd/git-repository.jpg)

Ennek az adattárnak négy fő szakasza van:

* A **karsablonok mappája,** amely az infrastruktúrát kódként tárolja
* A **hpc-alkalmazás** mappát, amely tartalmazza a bináris ffmpeg
* A **folyamatfolyamatok mappája,** amely a buildfolyamat definícióját tartalmazza.
* **Nem kötelező**: Az **ügyfél-alkalmazás** mappa, amely a .NET alkalmazás kódját tárolja. Ezt nem használjuk a mintában, de a saját projektben előfordulhat, hogy a HPC batch alkalmazás futtatásait egy ügyfélalkalmazáson keresztül szeretné végrehajtani.

> [!NOTE]
> Ez csak egy példa a kódbázis szerkezetére. Ez a megközelítés annak bizonyítására szolgál, hogy az alkalmazás, az infrastruktúra és a csővezeték-kód ugyanabban a tárházban van tárolva.

Most, hogy a forráskód be van állítva, megkezdhetjük az első buildet.

## <a name="continuous-integration"></a>Folyamatos integráció

[Az Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops)az Azure DevOps-szolgáltatásokon belül segít az alkalmazások buildelési, tesztelési és telepítési folyamatának megvalósításában.

Ebben a szakaszban a folyamat, tesztek általában futanak a kód érvényesítéséhez, és a szoftver megfelelő részeinek létrehozásához. A tesztek száma és típusa, valamint a futtatott további feladatok a szélesebb körű összeállítási és kiadási stratégiától függenek.

## <a name="preparing-the-hpc-application"></a>A HPC alkalmazás előkészítése

Ebben a példában a **hpc-alkalmazás** mappára összpontosítunk. A **hpc-alkalmazás** mappa az ffmpeg szoftver, amely az Azure Batch-fiókból fog futni.

1. Keresse meg az Azure DevOps-szervezet Builds szakaszát. Új **folyamat**létrehozása .

    ![Új összeállítási folyamat létrehozása](media/batch-ci-cd/new-build-pipeline.jpg)

1. A Build-folyamat létrehozásához két lehetősége van:

    a. [A Visual Designer használata](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Ennek használatához kattintson az **Új folyamat** lap "A vizuális tervező használata" gombjára.

    b. [YAML buildek használata](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). Új YAML-folyamatot hozhat létre az Azure Repos vagy a GitHub beállításra kattintva az Új folyamat lapon. Másik lehetőségként tárolhatja az alábbi példát a forrásvezérlőben, és hivatkozhat egy meglévő YAML-fájlra a Visual Designer elemre kattintva, majd a YAML sablon használatával.

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

1. Miután a build szükség szerint konfigurálva van, válassza **a & várólista mentése**lehetőséget. Ha a folyamatos integráció engedélyezve van (az **Eseményindítók** szakaszban), a build automatikusan aktiválódik, amikor egy új véglegesítést a tárház hoz létre, a buildben beállított feltételeknek való megfelelve.

    ![Példa meglévő buildelési folyamatra](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Élő ben megtekintheti a build az Azure DevOps-ban a build az Azure-folyamatok **buildelésszakaszának** navigálásával. Válassza ki a megfelelő buildet a builddefinícióból.

    ![Élő kimenetek megtekintése a buildből](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Ha egy ügyfélalkalmazást használ a HPC batch alkalmazás végrehajtásához, létre kell hoznia egy külön build-definíciót az adott alkalmazáshoz. Az [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) dokumentációjában számos útmutatóútmutatót talál.

## <a name="continuous-deployment"></a>Folyamatos üzembe helyezés

Az Azure Pipelines is használt az alkalmazás és az alapul szolgáló infrastruktúra üzembe helyezéséhez. [A kiadási folyamatok](https://docs.microsoft.com/azure/devops/pipelines/release) az az összetevő, amely lehetővé teszi a folyamatos üzembe helyezést, és automatizálja a kiadási folyamatot.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Az alkalmazás és az alapul szolgáló infrastruktúra telepítése

Az infrastruktúra üzembe helyezése számos lépéssel jár. Mivel összekapcsolt [sablonokat](../azure-resource-manager/templates/linked-templates.md)használtunk, ezeknek a sablonoknak nyilvános végpontról (HTTP vagy HTTPS) kell elérhetőknek lenniük. Ez lehet egy tárház a GitHubon, vagy egy Azure Blob Storage-fiók, vagy egy másik tárolási hely. A feltöltött sablonösszetevők biztonságosak maradhatnak, mivel privát módban tarthatók, de a megosztott hozzáférésű aláírás (SAS) jogkivonat valamilyen formában elérhetők. A következő példa bemutatja, hogyan telepíthet egy azure storage-blobsablonokkal rendelkező infrastruktúrát.

1. Hozzon létre egy **új kiadási definíciót**, és jelöljön ki egy üres definíciót. Ezután át kell neveznünk az újonnan létrehozott környezetet valami fontosra a csővezetékünk számára.

    ![Kezdeti kiadási folyamat](media/batch-ci-cd/Release-0.jpg)

1. Hozzon létre egy függőséget a buildelési folyamat a hpc-alkalmazás kimenetének lekérni.

    > [!NOTE]
    > Ismét vegye figyelembe a **Forrás alias**, mivel ez lesz szükség, ha feladatok jönnek létre a kiadási definíció.

    ![Hozzon létre egy műtermék-kapcsolatot a HPCApplicationPackage csomaggal a megfelelő buildfolyamatban](media/batch-ci-cd/Release-1.jpg)

1. Hozzon létre egy hivatkozást egy másik összetevő, ezúttal egy Azure-tármű. Ez a tárházban tárolt Erőforrás-kezelő sablonok eléréséhez szükséges. Mivel az Erőforrás-kezelő sablonjai nem igényelnek fordítást, nem kell lelökni őket egy buildfolyamaton keresztül.

    > [!NOTE]
    > Ismét vegye figyelembe a **Forrás alias**, mivel ez lesz szükség, ha feladatok jönnek létre a kiadási definíció.

    ![Az Azure-repók må±termã©k-ja-kapcsolat létrehozása](media/batch-ci-cd/Release-2.jpg)

1. Keresse meg a **változók szakaszt.** Javasoljuk, hogy hozzon létre számos változót a folyamatban, így nem adja meg ugyanazt az információt több tevékenységbe. Ezek a példában használt változók, és hogyan befolyásolják a központi telepítést.

    * **applicationStorageAccountName**: A HPC alkalmazás bináris fájljait tároló tárfiók neve
    * **batchAccountApplicationName**: Az alkalmazás neve az Azure Batch-fiókban
    * **batchAccountName**: Az Azure Batch-fiók neve
    * **batchAccountPoolName**: A feldolgozást lefolytató virtuális gépek készletének neve
    * **batchApplicationId:** Az Azure Batch-alkalmazás egyedi azonosítója
    * **batchApplicationVersion**: A kötegelt alkalmazás szemantikai verziója (azaz az ffmpeg binárisfájlok)
    * **hely**: A telepítendő Azure-erőforrások helye
    * **resourceGroupName**: A létrehozandó erőforráscsoport neve, és az erőforrások üzembe helyezése helye
    * **storageAccountName**: A csatolt Erőforrás-kezelő sablonok tárolására szolgáló tárfiók neve

    ![Példa az Azure Pipelines kiadásához beállított változókra](media/batch-ci-cd/Release-4.jpg)

1. Keresse meg a fejlesztői környezet feladatait. Az alábbi pillanatképben hat tevékenység látható. Ezek a feladatok: a tömörített ffmpeg fájlok letöltése, tárfiók központi telepítése a beágyazott Erőforrás-kezelő sablonok üzemeltetéséhez, az Erőforrás-kezelő sablonok másolása a tárfiókba, a kötegfiók és a szükséges függőségek központi telepítése, alkalmazás létrehozása a az Azure Batch-fiók, és töltse fel az alkalmazáscsomagot az Azure Batch-fiókba.

    ![Példa a HPC-alkalmazás Azure Batch számára történő kiadásához használt feladatokra](media/batch-ci-cd/Release-3.jpg)

1. Adja hozzá a **Letöltési folyamat műtermék (előzetes)** feladat, és állítsa be a következő tulajdonságokat:
    * **Megjelenítendő név:** Alkalmazáscsomag letöltése ügynöknek
    * **A letöltendő műtermék neve:** hpc-alkalmazás
    * **Letöltési útvonal:**$(System.DefaultWorkingDirectory)

1. Hozzon létre egy tárfiókot az összetevők tárolásához. A megoldásból egy meglévő tárfiók használható, de az önálló minta és a tartalom elkülönítése érdekében dedikált tárfiókot hozunk az összetevőkhöz (különösen az Erőforrás-kezelő sablonokhoz).

    Adja hozzá az **Azure Resource Group deployment feladatot,** és állítsa be a következő tulajdonságokat:
    * **Megjelenítendő név:** Tárfiók telepítése erőforrás-kezelősablonokhoz
    * **Azure-előfizetés:** Válassza ki a megfelelő Azure-előfizetést
    * **Művelet**: Erőforráscsoport létrehozása vagy frissítése
    * **Erőforráscsoport**: $(resourceGroupName)
    * **Helyszín**: $(hely)
    * **Sablon**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    * **Sablonparaméterek felülbírálása**: -accountName $(storageAccountName)

1. Töltse fel az összetevőket a forrásvezérlőből a tárfiókba. Ehhez egy Azure Pipeline-feladat van. Ennek a feladatnak a részeként a storage-fiók tároló URL-címe és a SAS-jogkivonat egy változó azure-folyamatok ban egy változó. Ez azt jelenti, hogy újra fel lehet használni ebben az ügynök fázisban.

    Adja hozzá az **Azure File Copy feladatot,** és állítsa be a következő tulajdonságokat:
    * **Forrás:** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/
    * **Azure-kapcsolat típusa**: Azure Resource Manager
    * **Azure-előfizetés:** Válassza ki a megfelelő Azure-előfizetést
    * **Cél típusa**: Azure Blob
    * **RM tárfiók**: $(storageAccountName)
    * **Tároló neve**: sablonok
    * **Tároló URI-ja**: templateContainerUri
    * **Tároló SAS-token**: templateContainerSasToken

1. Telepítse az orchestrator sablont. Hívja vissza az orchestrator sablon korábbi, észre fogod venni, hogy a SAS-jogkivonat on kívül a storage-fiók tároló URL-címének paraméterek voltak. Észre kell venni, hogy a szükséges változók az Erőforrás-kezelő sablonban vagy a változók szakaszában a kiadási definíció, vagy egy másik Azure Pipelines feladat (például az Azure Blob Copy feladat része) vannak beállítva.

    Adja hozzá az **Azure Resource Group deployment feladatot,** és állítsa be a következő tulajdonságokat:
    * **Megjelenítendő név:** Az Azure Batch telepítése
    * **Azure-előfizetés:** Válassza ki a megfelelő Azure-előfizetést
    * **Művelet**: Erőforráscsoport létrehozása vagy frissítése
    * **Erőforráscsoport**: $(resourceGroupName)
    * **Helyszín**: $(hely)
    * **Sablon**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    * **Sablonparaméterek felülbírálása:**```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Általános gyakorlat az Azure Key Vault-feladatok használata. Ha a szolgáltatásnév (kapcsolat az Azure-előfizetéshez) rendelkezik a megfelelő hozzáférési szabályzatok beállítása, letöltheti a titkokat egy Azure Key Vault, és a folyamat változókként használható. A titkos titok neve a társított értékkel együtt lesz beállítva. Például az sshPassword titkára $(sshPassword) szerepelhet a kiadási definícióban.

1. A következő lépések az Azure CLI hívása. Az első egy alkalmazás létrehozásához használható az Azure Batchben. és töltse fel a kapcsolódó csomagokat.

    Adja hozzá az **Azure CLI-feladatot,** és állítsa be a következő tulajdonságokat:
    * **Megjelenítendő név:** Alkalmazás létrehozása az Azure Batch-fiókban
    * **Azure-előfizetés:** Válassza ki a megfelelő Azure-előfizetést
    * **Parancsfájl helye**: Szövegközi parancsfájl
    * **Szövegközi parancsfájl:**```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. A második lépés a kapcsolódó csomagok feltöltésére szolgál az alkalmazásba. A mi esetünkben, az ffmpeg fájlokat.

    Adja hozzá az **Azure CLI-feladatot,** és állítsa be a következő tulajdonságokat:
    * **Megjelenítendő név:** Csomag feltöltése az Azure Batch-fiókba
    * **Azure-előfizetés:** Válassza ki a megfelelő Azure-előfizetést
    * **Parancsfájl helye**: Szövegközi parancsfájl
    * **Szövegközi parancsfájl:**```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Az alkalmazáscsomag verziószáma változóra van állítva. Ez akkor hasznos, ha a csomag korábbi verzióinak felülírása működik az Ön számára, és ha manuálisan szeretné szabályozni az Azure Batch-be leadott csomag verziószámát.

1. Hozzon létre egy új kiadást a **Kiadás > Új kiadás létrehozása**lehetőséget választva. Az aktiválást követően válassza ki az új kiadásra mutató hivatkozást az állapot megtekintéséhez.

1. Megtekintheti az ügynök élő kimenetét a környezet alatti **Naplók** gomb kiválasztásával.

    ![A kiadás állapotának megtekintése](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>A környezet tesztelése

A környezet beállítása után ellenőrizze, hogy a következő tesztek sikeresen elvégezhetők-e.

Csatlakozzon az új Azure Batch-fiókhoz az Azure CLI használatával egy PowerShell-parancssorból.

* Jelentkezzen be az Azure-fiókjába, `az login` és kövesse az utasításokat a hitelesítéshez.
* Most hitelesítse a Batch-fiókot:`az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Az elérhető alkalmazások felsorolása

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Ellenőrizze, hogy a készlet érvényes-e

```azurecli
az batch pool list
```

Figyelje meg `currentDedicatedNodes` a parancs kimenetének értékét. Ezt az értéket a következő vizsgálatban módosítjuk.

#### <a name="resize-the-pool"></a>A készlet átméretezése

A készlet átméretezése, hogy rendelkezésre álljanak a feladat- és feladatteszteléshez rendelkezésre álló számítási csomópontok, ellenőrizze a készletlista parancsával az aktuális állapotot, amíg az átméretezés befejeződik, és rendelkezésre állnak a csomópontok

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>További lépések

A cikk mellett két oktatóanyag is található, amelyek a .NET és a Python használatával ffmpeg-et használnak. Ezek az útmutatók további információt arról, hogyan lehet kölcsönhatásba egy Batch-fiók egy egyszerű alkalmazás.

* [Párhuzamos számítási feladat futtatása az Azure Batch használatával a Python API használatával](tutorial-parallel-python.md)
* [Párhuzamos számítási feladatok futtatása az Azure Batch használatával a .NET API használatával](tutorial-parallel-dotnet.md)
