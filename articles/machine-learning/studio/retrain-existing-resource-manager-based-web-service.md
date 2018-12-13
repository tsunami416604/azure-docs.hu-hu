---
title: Meglévő prediktív Studio webszolgáltatás újratanítása – Azure Machine Learning Studióban |} A Microsoft Docs
description: Ismerje meg, hogyan modellek szoftveres átképezése és frissíteni a webszolgáltatást a újonnan betanított modell használata az Azure Machine Learning Studióban.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: cc4c26a2-5672-4255-a767-cfd971e46775
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.openlocfilehash: d24cfd8dce42ee920c87fa2c3593a860468a6160
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53250552"
---
# <a name="retrain-an-existing-predictive-azure-machine-learning-studio-web-service"></a>Prediktív Azure Machine Learning Studióba meglévő webszolgáltatás újratanítása

Ez a dokumentum azt ismerteti, hogyan megőrzési az alábbi forgatókönyvet:

* Betanítási kísérlet és a egy prediktív kísérletet, webszolgáltatásig központilag telepített rendelkezik.
* Új adatokat, amelyet az hajthat végre, a kiértékelés prediktív webszolgáltatásait.

> [!NOTE]
> Egy új webszolgáltatás üzembe helyezéséhez rendelkeznie megfelelő engedélyekkel, amelyhez az előfizetésben, a web Service szolgáltatásának telepítése. További információ: [egy webszolgáltatás, az Azure Machine Learning Web Services portál használata kezelheti](manage-new-webservice.md).

A meglévő webszolgáltatás és a kísérletek kezdve kell kövesse az alábbi lépéseket:

1. A modell frissítése.
   1. Módosítsa a betanítási kísérlet, hogy a web service bemeneteit és kimeneteit.
   2. Helyezze üzembe a tanítási kísérlet megőrzési webszolgáltatásként.
   3. Újratanítás a modell a tanítási kísérlet Batch Execution Service (BES) használja.
2. Az Azure Machine Learning PowerShell-parancsmagok használatával frissítheti a prediktív kísérletet.
   1. Jelentkezzen be az Azure Resource Manager-fiókot.
   2. A webszolgáltatás-definíciójának beolvasása.
   3. Exportálja a webszolgáltatás-definíciójának JSON-fájlként.
   4. Frissítse a ilearner blob, a JSON-mutató hivatkozást.
   5. A JSON importálja egy webszolgáltatás-definíciójának.
   6. A webszolgáltatás egy új webszolgáltatás-definíciójának frissítése.

## <a name="deploy-the-training-experiment"></a>A betanítási kísérlet üzembe helyezése
A tanítási kísérlet megőrzési webszolgáltatásként üzembe helyezéséhez hozzá kell adnia webes szolgáltatás bemeneteit és kimeneteit a modellbe. Csatlakozzon egy *webes szolgáltatás kimeneti* modult a kísérletvászonra *[tanítási modell][train-model]* modult, engedélyezi a betanítási kísérlet használhatja a prediktív kísérletet új betanított modell létrehozásához. Ha rendelkezik egy *Evaluate Model* modult, webes szolgáltatás kimeneti az értékelési eredmények lekérdezése kimenetként is hozzáadhat.

A betanítási kísérlet frissítése:

1. Csatlakozás egy *webes szolgáltatás bemeneti* modult a bevitt adatok (például egy *Clean Missing Data* modul). Általában érdemes győződjön meg arról, hogy a bemeneti adatok feldolgozása a ugyanúgy, mint az eredeti betanítási adatok.
2. Csatlakozás egy *webes szolgáltatás kimeneti* modul kimenetét a *tanítási modell* modul.
3. Ha rendelkezik egy *Evaluate Model* modul, és azt szeretné, a kiértékelési eredményeket, csatlakozzon egy *webes szolgáltatás kimeneti* modul kimenetét a *Evaluate Model* modul.

Futtassa a kísérletet.

Ezután telepítenie kell a tanítási kísérlet webes szolgáltatás, amely a betanított modell és a modell kiértékelésének eredménye hoz létre.

A kísérlet vászon alján kattintson **webszolgáltatás beállítása**, majd válassza ki **[Új] Web Service telepítése**. Az Azure Machine Learning Web Services portálon megnyitja a **webszolgáltatás üzembe helyezése** lapot. Adja meg a webszolgáltatás nevét, egy támogatási csomag kiválasztása, és kattintson **telepítés**. Használhatja a kötegelt végrehajtási metódus csak betanított modellek létrehozásához.

## <a name="retrain-the-model-with-new-data-by-using-bes"></a>A modell új adatokkal újratanítás BES használatával
Ebben a példában használunk a C# megőrzési-alkalmazás létrehozása. Használhatja a Python vagy R mintakód ennek a feladatnak.

A megőrzési API-k meghívásához:

1. Hozzon létre egy C# a Visual Studio-Konzolalkalmazás: **Új** > **projekt** > **Visual C#**   >  **Windows klasszikus Asztalialkalmazás**  >   **Console App (.NET Framework)**.
2. Jelentkezzen be a Machine Learning webszolgáltatások portálján.
3. Kattintson a webszolgáltatás, amelyet dolgozik.
4. Kattintson a **felhasználása**.
5. Alsó részén a **felhasználás** lap a **mintakód** területén kattintson **Batch**.
6. A kötegelt végrehajtás C# mintakód másolja, és illessze be a Program.cs fájlt. Győződjön meg arról, hogy a névtér érintetlen marad.

Adja hozzá a NuGet-csomag Microsoft.AspNet.WebApi.Client, teljesítjük, a megjegyzéseket. Microsoft.WindowsAzure.Storage.dll mutató hivatkozás hozzáadásához először meg kell telepítenie a [Azure Storage-szolgáltatáshoz készült ügyféloldali kódtára](https://www.nuget.org/packages/WindowsAzure.Storage).

Az alábbi képernyőfelvételen a **felhasználás** oldal az Azure Machine Learning Web Services portálon.

![Oldal használata][1]

### <a name="update-the-apikey-declaration"></a>Frissítés a apikey tulajdonsággal végzett tesztelése nyilatkozat
Keresse meg a **apikey tulajdonsággal végzett tesztelése** nyilatkozat:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Az a **alapvető fogyasztási adatai** szakaszában a **felhasználás** lapon, keresse meg az elsődleges kulcsot, és másolja a **apikey tulajdonsággal végzett tesztelése** nyilatkozatot.

### <a name="update-the-azure-storage-information"></a>Az Azure Storage-adatok frissítése
A BES-mintakód feltölt egy fájlt egy helyi meghajtóról (például "C:\temp\CensusIpnput.csv") az Azure Storage, feldolgozza őket, majd az eredmények visszaírja az Azure Storage.

Miután is futtathatja a kísérletet, az eredményül kapott munkafolyamat a következőhöz hasonló lesz:

![Eredményül kapott munkafolyamat futtatása után][4]

1. Jelentkezzen be az Azure portálra.
2. A bal oldali navigációs oszlopban kattintson **további szolgáltatások**, keressen **tárfiókok**, és válassza ki azt.
3. Storage-fiókok listájából válassza ki a retrained modell tárolásához.
4. A bal oldali navigációs oszlopban kattintson **hozzáférési kulcsok**.
5. Másolja ki és mentse a **elsődleges elérési kulcs**.
6. A bal oldali navigációs oszlopban kattintson **tárolók**.
7. Válassza ki egy meglévő tárolót, vagy hozzon létre egy új, és mentse a nevét.

Keresse meg a *StorageAccountName*, *StorageAccountKey*, és *StorageContainerName* nyilatkozatok, és frissítse az értékeket a Portalról mentett.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Is biztosítania kell, hogy a bemeneti fájl a kódban megadott helyen érhető el.

### <a name="specify-the-output-location"></a>Adja meg a kimeneti helyet
Ha a kimeneti helyet ad meg a kérelem hasznos adatainak, a megadott fájl kiterjesztése *RelativeLocation* kell megadni, `ilearner`. Lásd a következő példát:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Az alábbiakban látható egy példa kimenet megőrzési:

![Kimeneti átképezési][6]

## <a name="evaluate-the-retraining-results"></a>Megőrzési eredmények értékelése
Az alkalmazás futtatásakor a kimenete tartalmazza az URL-cím és a közös hozzáférésű jogosultságkódok jogkivonat értékelési eredmények eléréséhez szükséges.

Megtekintheti a retrained modell teljesítmény eredményeinek kombinálásával a *BaseLocation*, *RelativeLocation*, és *SasBlobToken* a kimenetieredményei*output2* (ahogyan az az előző megőrzési kimeneti képen látható), és illessze be a teljes URL-CÍMÉT a böngésző címsorába.

Vizsgálja meg az eredményeket megállapítani, hogy az újonnan betanított modell végrehajt elég jól lecserélheti a meglévőt.

Másolás a *BaseLocation*, *RelativeLocation*, és *SasBlobToken* a kimeneti eredmények közül.

## <a name="retrain-the-web-service"></a>A webszolgáltatás újratanítása
Ha egy új webszolgáltatás újratanítása, a prediktív webszolgáltatás-definíciójának való hivatkozáshoz az új betanított modell frissítenie. A webszolgáltatás-definíciójának egy belső ábrázolása a webszolgáltatás a betanított modell, ezért nem módosítható közvetlenül. Győződjön meg arról, hogy vannak-e a webszolgáltatás-definíciójának beolvasása a prediktív kísérletet, és nem a betanítási kísérlet.

## <a name="sign-in-to-azure-resource-manager"></a>Jelentkezzen be az Azure Resource Manager
Meg kell először jelentkezzen be Azure-fiókjába, a PowerShell környezetben a [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) parancsmagot.

## <a name="get-the-web-service-definition-object"></a>A webszolgáltatás-definíciójának objektum lekérése
Ezután kérdezze le a webszolgáltatás-definíciójának objektum meghívásával a [Get-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservice) parancsmagot.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Meglévő webszolgáltatás, az erőforráscsoport nevének megállapításához futtassa a Get-AzureRmMlWebService parancsmag a webes szolgáltatások megjelenítéséhez az előfizetésében paraméterek nélkül. Keresse meg a webszolgáltatás, és tekintse meg a webszolgáltatás-azonosítót. Az erőforráscsoport neve nem az azonosító, a negyedik eleme után a *resourceGroups* elemet. A következő példában az erőforráscsoport nevének alapértelmezett-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Azt is megteheti az erőforráscsoport nevének egy létező webes szolgáltatás határozza meg, jelentkezzen be az Azure Machine Learning Web Services portálon. Válassza ki a web service. Az erőforráscsoport nevét a web service URL-CÍMÉT a minden ötödik eleme után a *resourceGroups* elemet. A következő példában az erőforráscsoport nevének alapértelmezett-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>A webszolgáltatás-definíciójának objektum exportálása JSON-fájlként
Az újonnan betanított modell használatára a betanított modell definícióját módosításához először használjon a [Export-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/export-azurermmlwebservice) JSON-formátumú fájlba exportálhatja, a parancsmag.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Frissítés a ilearner blob mutató hivatkozás
Az eszközök, keresse meg a [betanított modell], frissítse a *uri* értékét a *locationInfo* ilearner BLOB URI-azonosítójú csomópont. Az URI egyesítésével jön létre a *BaseLocation* és a *RelativeLocation* a hívás átképezési BES kimenetéből származó.

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

## <a name="import-the-json-into-a-web-service-definition-object"></a>A JSON importálhat egy webszolgáltatás-definíciójának objektum
Kell használnia a [Import-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/import-azurermmlwebservice) parancsmag a módosított JSON-fájlt vissza alakítható át egy webszolgáltatás-definíciójának objektum, amely segítségével a predicative kísérletben frissíteni.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>A web service frissítése
Végül a [Update-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/update-azurermmlwebservice) parancsmag, amellyel frissíthetőek a prediktív kísérletet.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

[1]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
