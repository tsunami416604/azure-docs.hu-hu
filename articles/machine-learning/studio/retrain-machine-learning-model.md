---
title: Webszolgáltatás újratanítása
titleSuffix: ML Studio (classic) - Azure
description: Ismerje meg, hogyan frissítheti a webszolgáltatást egy újonnan betanított gépi tanulási modell használatához az Azure Machine Learning Studio (klasszikus) alkalmazásban.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/14/2019
ms.openlocfilehash: 218c1c98a2ed775ae86c1657156991879708cc7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217937"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Gépi tanulási modell újratanítása és üzembe helyezése

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Az átképzés az egyik módja annak, hogy a gépi tanulási modellek pontosak maradjanak, és a rendelkezésre álló legrelevánsabb adatok on alapuljanak. Ez a cikk bemutatja, hogyan újratanítása és üzembe helyezése egy gépi tanulási modell, mint egy új webszolgáltatás a Studio (klasszikus). Ha klasszikus webszolgáltatást szeretne újraképezni, [tekintse meg ezt az útmutató cikket.](retrain-classic-web-service.md)

Ez a cikk feltételezi, hogy már rendelkezik egy prediktív webszolgáltatás üzembe helyezése. Ha még nem rendelkezik prediktív webszolgáltatással, [itt megtudhatja, hogyan helyezhet üzembe egy Studio (klasszikus) webszolgáltatást.](deploy-a-machine-learning-web-service.md)

Az alábbi lépésekkel újrataníthatja és üzembe helyezheti a gépi tanulás új webszolgáltatását:

1. **Átképzési webszolgáltatás** telepítése
1. Új modell betanítása az **átképzési webszolgáltatás** használatával
1. A meglévő **prediktív kísérlet** frissítése az új modell használatához

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>Az átképzés webszolgáltatás ának üzembe helyezése

Az átképzés webszolgáltatás lehetővé teszi, hogy újratanítsa a modellt egy új paraméterkészlettel, például új adatokkal, és mentse későbbre. Amikor egy **webszolgáltatás-kimenetet** csatlakoztat egy **betanítási modellhez,** a betanítási kísérlet egy új modellt ad ki, amelyet használni szeretne.

Az átképzési webszolgáltatás üzembe helyezéséhez kövesse az alábbi lépéseket:

1. **Webszolgáltatás-beviteli** modul csatlakoztatása az adatbevitelhez. Általában azt szeretné, hogy a bemeneti adatok feldolgozása ugyanúgy, mint az eredeti betanítási adatok.
1. Csatlakoztasson egy **webszolgáltatás kimeneti** modulját a **betanítási modell**kimenetéhez.
1. Ha **modellkiértékelés** modullal rendelkezik, csatlakoztathat egy **webszolgáltatás kimeneti** modulját a kiértékelési eredmények kimenetéhez
1. Futtassa le a kísérletet.

    A kísérlet futtatása után az eredményül kapott munkafolyamatnak az alábbi képhez kell hasonlónak lennie:

    ![Az eredményül kapott munkafolyamat](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Most üzembe helyezi a betanítási kísérlet átképzési webszolgáltatásként, amely egy betanított modell és modell értékelési eredmények et ad ki.

1. A kísérleti vászon alján kattintson a **Webszolgáltatás beállítása elemre.**
1. Válassza **a Webszolgáltatás telepítése [Új]** lehetőséget. Az Azure Machine Learning webszolgáltatások portálja megnyílik a **webszolgáltatás telepítése** lapra.
1. Írja be a webszolgáltatás nevét, és válasszon fizetési tervet.
1. Válassza az **Üzembe helyezés** lehetőséget.

## <a name="retrain-the-model"></a>A modell újratanítása

Ebben a példában c# használatával hozzuk létre az átképzési alkalmazást. A feladat végrehajtásához python- vagy R-mintakódot is használhat.

Az átképzési API-k hívásához kövesse az alábbi lépéseket:

1. Hozzon létre egy C# konzolalkalmazást a Visual Studio: **New** > **Project** > **Visual C#** > Windows Classic**Desktop** > **Console App (.NET Framework) alkalmazásban.**
1. Jelentkezzen be a Machine Learning webszolgáltatások portáljára.
1. Kattintson arra a webszolgáltatásra, amelynek a segítségével dolgozik.
1. Kattintson **a Felhasználás gombra.**
1. A **Felhasználás** lap **Mintakód** szakaszában kattintson a **Köteg gombra.**
1. Másolja a c# mintakódot a kötegelt végrehajtáshoz, és illessze be a Program.cs fájlba. Győződjön meg arról, hogy a névtér érintetlen marad.

Adja hozzá a Microsoft.AspNet.WebApi.Client NuGet csomagot a megjegyzésekben megadottak szerint. Ha hozzá szeretné adni a hivatkozást a Microsoft.WindowsAzure.Storage.dll fájlhoz, előfordulhat, hogy telepítenie kell az [Azure Storage-szolgáltatások ügyfélkönyvtárát.](https://www.nuget.org/packages/WindowsAzure.Storage)

Az alábbi képernyőképen látható a **Felhasználás** lap az Azure Machine Learning webszolgáltatások portálon.

![Felhasználás lap](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>Az apikey deklaráció frissítése

Keresse meg az **apikey** deklarációt:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

A **Felhasználás** lap **Alapszintű felhasználási adatok** szakaszában keresse meg az elsődleges kulcsot, és másolja az **apikey** deklarációba.

### <a name="update-the-azure-storage-information"></a>Az Azure Storage-adatok frissítése

A BES-mintakód feltölt egy fájlt egy helyi meghajtóról (például "C:\temp\CensusInput.csv") az Azure Storage-ba, feldolgozza, és visszaírja az eredményeket az Azure Storage-ba.

1. Bejelentkezés az Azure Portalra
1. A bal oldali navigációs oszlopban kattintson a **További szolgáltatások**elemre, keresse meg a **Tárfiókok at,** és jelölje ki.
1. A tárfiókok listájából válasszon egyet az újrabetanított modell tárolásához.
1. A bal oldali navigációs oszlopban kattintson az **Access billentyűk**elemre .
1. Másolja és mentse az **elsődleges hozzáférési kulcsot**.
1. A bal oldali navigációs oszlopban kattintson a **Blobs gombra.**
1. Jelöljön ki egy meglévő tárolót, vagy hozzon létre egy újat, és mentse a nevet.

Keresse meg a *StorageAccountName*, *StorageAccountKey*és *StorageContainerName* deklarációkat, és frissítse a portálról mentett értékeket.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Azt is meg kell győződnie arról, hogy a bemeneti fájl elérhető a kódban megadott helyen.

### <a name="specify-the-output-location"></a>A kimeneti hely megadása

Ha a kimeneti helyet a Kérelem hasznos adatában adja meg, a *RelativeLocation* mezőben megadott fájlkiterjesztést a következőként `ilearner`kell megadni: .

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Íme egy példa a kimenet átképzésére:

![Átképzés kimenete](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>Az átképzési eredmények értékelése

Az alkalmazás futtatásakor a kimenet tartalmazza az URL-címet és a megosztott hozzáférés-aláírások jogkivonatot, amelyek szükségesek a kiértékelési eredmények eléréséhez.

Az újrabetanított modell teljesítményeredményeit az újrabetanított modell teljesítményének kombinálásával láthatja a *BaseLocation*, *RelativeLocation*és *SasBlobToken* a *kimeneti eredményekből2* és a teljes URL beillesztése a böngésző címsorába.

Vizsgálja meg az eredményeket annak megállapítására, hogy az újonnan betanított modell jobban teljesít-e, mint a meglévő.

Mentse a *BaseLocation*, *RelativeLocation*és *SasBlobToken* a kimeneti eredmények.

## <a name="update-the-predictive-experiment"></a>A prediktív kísérlet frissítése

### <a name="sign-in-to-azure-resource-manager"></a>Bejelentkezés az Azure Resource Manager bejelentkezése

Először jelentkezzen be az Azure-fiókjába a PowerShell-környezetből a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag használatával.

### <a name="get-the-web-service-definition-object"></a>A Webszolgáltatás-definícióobjektum beszereznie

Ezután a [Get-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservice) parancsmag meghívásával kapjuk meg a webszolgáltatás-definíciós objektumot.

    $wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Egy meglévő webszolgáltatás erőforráscsoport nevének meghatározásához futtassa a Get-AzMlWebService parancsmaca paraméterek nélkül a webszolgáltatások megjelenítéséhez az előfizetésben. Keresse meg a webszolgáltatást, majd tekintse meg a webszolgáltatás azonosítóját. Az erőforráscsoport neve az azonosító negyedik eleme, közvetlenül az *resourceGroups* elem után. A következő példában az erőforráscsoport neve Default-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Azt is megteheti, hogy egy meglévő webszolgáltatás erőforráscsoport nevét határozza meg, jelentkezzen be az Azure Machine Learning Web Services portálra. Válassza ki a webszolgáltatást. Az erőforráscsoport neve a webszolgáltatás URL-címének ötödik eleme, közvetlenül a *resourceGroups* elem után. A következő példában az erőforráscsoport neve Default-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237

### <a name="export-the-web-service-definition-object-as-json"></a>A webszolgáltatás-definíciós objektum exportálása JSON-ként

A betanított modell újonnan betanított modell használatára vonatkozó definíciójának módosításához először az [Export-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) parancsmag gal kell használnia azt Egy JSON formátumú fájlba.

    Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

### <a name="update-the-reference-to-the-ilearner-blob"></a>Az ilearner blobra mutató hivatkozás frissítése

Az eszközök keresse meg a [betanított modell], frissítse az uri értékét a locationInfo csomópont az URI az ilearner blob uri.In the assets, locate the [trained model], update the *uri* value in the *locationInfo* node with the URI of the ilearner blob. Az URI a *BaseLocation* és a *RelativeLocation* a BES átképzési hívás kimenetének kombinálásával jön létre.

     "asset3": {
        "name": "Retrain Sample [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

### <a name="import-the-json-into-a-web-service-definition-object"></a>A JSON importálása webszolgáltatás-definíciós objektumba

Az [Import-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/import-azmlwebservice) parancsmag segítségével konvertálja a módosított JSON-fájlt webszolgáltatás-definíciós objektummá, amely segítségével frissítheti a predicative kísérletet.

    $wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"

### <a name="update-the-web-service"></a>A webszolgáltatás frissítése

Végül használja az [Update-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/update-azmlwebservice) parancsmaga a prediktív kísérlet frissítéséhez.

    Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a rról, hogyan kezelheti a webszolgáltatásokat, illetve hogyan követheti nyomon a több kísérletfuttatást, olvassa el az alábbi cikkeket:

* [A webszolgáltatások portál jának felfedezése](manage-new-webservice.md)
* [Kísérletismétlések kezelése](manage-experiment-iterations.md)
