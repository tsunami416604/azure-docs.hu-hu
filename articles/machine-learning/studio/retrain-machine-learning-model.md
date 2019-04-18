---
title: Újratanítás PowerShell és a egy webszolgáltatás üzembe helyezése
titleSuffix: Azure Machine Learning Studio
description: Ismerje meg, hogyan frissíthető egy újonnan betanított gépi tanulási modellt az Azure Machine Learning Studióban használni egy webszolgáltatás.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/14/2019
ms.openlocfilehash: 903f2700ad127c9bcc69e69ee125ba62fccf52e0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59051631"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Újratanítás PowerShell és a egy gépi tanulási modell üzembe helyezése

Átképezési biztosítható a machine learning-modellek maradjon pontos és a legfontosabb rendelkezésre álló adatok alapján. Ez a cikk bemutatja, hogyan újratanítása, és a egy gépi tanulási modellt a Studióban új webszolgáltatásként üzembe. Ha szeretne egy klasszikus webszolgáltatás újratanítása [a cikkben található útmutató megtekintéséhez.](retrain-classic-web-service.md)

Ez a cikk feltételezi, hogy már telepített egy prediktív webszolgáltatás. Ha még nem rendelkezik egy prediktív webszolgáltatás [megtudhatja, hogyan helyezhet üzembe egy Studio-webszolgáltatás itt.](publish-a-machine-learning-web-service.md)

Újratanítás PowerShell és a egy machine learning új webszolgáltatás üzembe helyezése ezen lépés végrehajtásával fogjuk:

1. Üzembe helyezése egy **átképezési webszolgáltatás**
1. Egy új modell használatával betanítása a **átképezési webszolgáltatás**
1. Frissítse a meglévő **prediktív kísérletté** az új modell használata

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>A megőrzési webszolgáltatás üzembe helyezése

A megőrzési webszolgáltatás lehetővé teszi egy új készlet új adatok, például a paraméterek a modellt újbóli betanítás után későbbi használatra mentse azt. Amikor kapcsolódik egy **webes szolgáltatás kimeneti** , egy **tanítási modell**, a tanítási kísérlet kimenete egy új modellt kell használnia.

Használja az alábbi lépéseket egy megőrzési webszolgáltatás üzembe helyezéséhez:

1. Csatlakozás egy **webes szolgáltatás bemeneti** modul bemeneti az adatokhoz. Általában érdemes győződjön meg arról, hogy a bemeneti adatok feldolgozása a ugyanúgy, mint az eredeti betanítási adatok.
1. Csatlakozás egy **webes szolgáltatás kimeneti** modul kimenetét a **tanítási modell**.
1. Ha rendelkezik egy **Evaluate Model** csatlakoztathatja a modul, egy **webes szolgáltatás kimeneti** modul kimeneti a kiértékelésének eredménye
1. Futtassa a kísérletet.

    Miután is futtathatja a kísérletet, az eredményül kapott munkafolyamat az alábbi képhez hasonló lesz:

    ![Eredményül kapott munkafolyamat](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Most már telepítheti a tanítási kísérlet megőrzési webszolgáltatásként, amely megjeleníti a betanított modell és a modell kiértékelésének eredménye.

1. A kísérlet vászon alján kattintson **webszolgáltatás beállítása**
1. Válassza ki **[Új] a Web Service szolgáltatásának telepítése**. Az Azure Machine Learning Web Services portálon megnyitja a **webszolgáltatás üzembe helyezése** lapot.
1. Adjon meg egy nevet a webszolgáltatás és a egy támogatási csomag kiválasztása.
1. Válassza az **Üzembe helyezés** lehetőséget.

## <a name="retrain-the-model"></a>A modell újratanítása

Ebben a példában használunk a C# megőrzési-alkalmazás létrehozása. Használhatja a Python vagy R mintakód ennek a feladatnak.

Használja az alábbi lépéseket a megőrzési API-k meghívásához:

1. Hozzon létre egy C# a Visual Studio-Konzolalkalmazás: **Új** > **projekt** > **Visual C#**   >  **Windows klasszikus Asztalialkalmazás**  >   **Console App (.NET Framework)**.
1. Jelentkezzen be a Machine Learning webszolgáltatások portálján.
1. Kattintson a webszolgáltatás, amelyet dolgozik.
1. Kattintson a **felhasználása**.
1. Alsó részén a **felhasználás** lap a **mintakód** területén kattintson **Batch**.
1. A kötegelt végrehajtás C# mintakód másolja, és illessze be a Program.cs fájlt. Győződjön meg arról, hogy a névtér érintetlen marad.

Adja hozzá a NuGet-csomag Microsoft.AspNet.WebApi.Client, teljesítjük, a megjegyzéseket. Microsoft.WindowsAzure.Storage.dll mutató hivatkozás hozzáadása, előfordulhat, hogy telepítenie kell a [Azure Storage-szolgáltatáshoz készült ügyféloldali kódtára](https://www.nuget.org/packages/WindowsAzure.Storage).

Az alábbi képernyőfelvételen a **felhasználás** oldal az Azure Machine Learning Web Services portálon.

![Oldal használata](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>Frissítés a apikey tulajdonsággal végzett tesztelése nyilatkozat

Keresse meg a **apikey tulajdonsággal végzett tesztelése** nyilatkozat:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Az a **alapvető fogyasztási adatai** szakaszában a **felhasználás** lapon keresse meg az elsődleges kulcsot, és másolja a **apikey tulajdonsággal végzett tesztelése** nyilatkozatot.

### <a name="update-the-azure-storage-information"></a>Az Azure Storage-adatok frissítése

A BES-mintakód feltölt egy fájlt egy helyi meghajtóról (például "C:\temp\CensusInput.csv") az Azure Storage, feldolgozza őket, majd az eredmények visszaírja az Azure Storage.

1. Bejelentkezés az Azure Portalra
1. A bal oldali navigációs oszlopban kattintson **további szolgáltatások**, keressen **tárfiókok**, és válassza ki azt.
1. Storage-fiókok listájából válassza ki a retrained modell tárolásához.
1. A bal oldali navigációs oszlopban kattintson **hozzáférési kulcsok**.
1. Másolja ki és mentse a **elsődleges elérési kulcs**.
1. A bal oldali navigációs oszlopban kattintson **tárolók**.
1. Válassza ki egy meglévő tárolót, vagy hozzon létre egy új, és mentse a nevét.

Keresse meg a *StorageAccountName*, *StorageAccountKey*, és *StorageContainerName* nyilatkozatok, és frissítse az értékeket a Portalról mentett.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Is biztosítania kell, hogy a bemeneti fájl a kódban megadott helyen érhető el.

### <a name="specify-the-output-location"></a>Adja meg a kimeneti helyet

Ha a kimeneti helyet ad meg a kérelem hasznos adatainak, a megadott fájl kiterjesztése *RelativeLocation* kell megadni, `ilearner`.

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Íme egy példa kimenet megőrzési:

![Kimeneti átképezési](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>Megőrzési eredmények értékelése

Az alkalmazás futtatásakor a kimenete tartalmazza az URL-cím és a közös hozzáférésű jogosultságkódok jogkivonat értékelési eredmények eléréséhez szükséges.

Megtekintheti a retrained modell teljesítmény eredményeinek kombinálásával a *BaseLocation*, *RelativeLocation*, és *SasBlobToken* a kimenetieredményei*output2* és illessze be a teljes URL-CÍMÉT a böngésző címsorába.

Vizsgálja meg az eredményeket, és ellenőrizze, ha az újonnan betanított modell jobban, mint a meglévőt végez.

Mentse a *BaseLocation*, *RelativeLocation*, és *SasBlobToken* a kimeneti eredmények közül.

## <a name="update-the-predictive-experiment"></a>Frissítés a prediktív kísérletté

### <a name="sign-in-to-azure-resource-manager"></a>Jelentkezzen be az Azure Resource Manager

Első lépésként jelentkezzen be Azure-fiókjába, a PowerShell környezetben a [Connect-AzAccount](/powershell/module/az.profile/connect-azaccount) parancsmagot.

### <a name="get-the-web-service-definition-object"></a>A webszolgáltatás-definíciójának objektum lekérése

Ezután kérdezze le a webszolgáltatás-definíciójának objektum meghívásával a [Get-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservice) parancsmagot.

    $wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Meglévő webszolgáltatás, az erőforráscsoport nevének megállapításához futtassa a Get-AzMlWebService parancsmag a webes szolgáltatások megjelenítéséhez az előfizetésében paraméterek nélkül. Keresse meg a webszolgáltatás, és tekintse meg a webszolgáltatás-azonosítót. Az erőforráscsoport neve nem az azonosító, a negyedik eleme után a *resourceGroups* elemet. A következő példában az erőforráscsoport nevének alapértelmezett-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Azt is megteheti az erőforráscsoport nevének egy létező webes szolgáltatás határozza meg, jelentkezzen be az Azure Machine Learning Web Services portálon. Válassza ki a web service. Az erőforráscsoport nevét a web service URL-CÍMÉT a minden ötödik eleme után a *resourceGroups* elemet. A következő példában az erőforráscsoport nevének alapértelmezett-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237

### <a name="export-the-web-service-definition-object-as-json"></a>A webszolgáltatás-definíciójának objektum exportálása JSON-fájlként

Az újonnan betanított modell használatára a betanított modell definícióját módosításához először használjon a [Export-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) JSON-formátumú fájlba exportálhatja, a parancsmag.

    Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

### <a name="update-the-reference-to-the-ilearner-blob"></a>Frissítés a ilearner blob mutató hivatkozás

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

### <a name="import-the-json-into-a-web-service-definition-object"></a>A JSON importálhat egy webszolgáltatás-definíciójának objektum

Használja a [Import-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/import-azmlwebservice) parancsmag a módosított JSON-fájlt vissza alakítható át egy webszolgáltatás-definíciójának objektum, amely segítségével a predicative kísérletben frissíteni.

    $wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"

### <a name="update-the-web-service"></a>A web service frissítése

Végül a [Update-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/update-azmlwebservice) parancsmag, amellyel frissíthetőek a prediktív kísérletet.

    Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

## <a name="next-steps"></a>További lépések

Többet webszolgáltatások kezelése, vagy nyomon követheti, több kísérletek Futtatás kapcsolatban, tekintse meg a következő cikkeket:

* [A Web Services portál felfedezése](manage-new-webservice.md)
* [Kísérletismétlések kezelése](manage-experiment-iterations.md)
