---
title: "Meglévő prediktív webszolgáltatás újratanítása |} Microsoft Docs"
description: "Megtudhatja, hogyan működik a modell, és frissíti a az újonnan betanított modell használatára az Azure Machine Learning webszolgáltatás."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: cc4c26a2-5672-4255-a767-cfd971e46775
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: v-donglo
ms.openlocfilehash: e7663f931594c0626a173562b846f3f9324d8ba3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="retrain-an-existing-predictive-web-service"></a>Meglévő prediktív webszolgáltatás újratanítása
Ez a dokumentum ismerteti a megőrzési folyamata a következő forgatókönyvet:

* Rendelkezik egy tanítási kísérletet, és egy prediktív kísérletté központilag telepített operationalized webszolgáltatásként.
* Új adatokat, amelyet a prediktív webszolgáltatás a pontozási elvégzéséhez.

> [!NOTE] 
> Egy új webszolgáltatás-bővítmény telepítése, megfelelő engedélyekkel kell rendelkeznie, amelyhez az előfizetést, a webszolgáltatás telepítése. További információ: [kezelése az Azure Machine Learning webszolgáltatások portál használatával egy webszolgáltatás-bővítmény](manage-new-webservice.md). 

A meglévő webszolgáltatás és kísérletek verziótól kezdődően kell kövesse az alábbi lépéseket:

1. A modell frissítése.
   1. Módosítsa a tanítási kísérletet, hogy a webszolgáltatás bemenetei és kimenetei lehetővé.
   2. Telepítse a tanítási kísérletet megőrzési webszolgáltatásként.
   3. A tanítási kísérletet kötegelt végrehajtási szolgáltatás (BES) segítségével a modell működik.
2. Az Azure Machine Learning PowerShell-parancsmagok használatával frissítheti a prediktív kísérletté.
   1. Jelentkezzen be Azure Resource Manager-fiókját.
   2. A webszolgáltatás-definíciójának beolvasása.
   3. Exportálja a webszolgáltatás-definíciójának JSON-ként.
   4. Frissítse a JSON-ban a ilearner blobra mutató hivatkozás.
   5. A JSON importálnia kell a webszolgáltatás-definíciójának.
   6. A webszolgáltatás frissítése egy új webszolgáltatás-definíciójának.

## <a name="deploy-the-training-experiment"></a>A tanítási kísérletet telepítése
A tanítási kísérletet megőrzési webszolgáltatásként telepítéséhez hozzá kell adnia a webszolgáltatás bemenetei és kimenetei a modellben. Csatlakozzon egy *webes szolgáltatás kimeneti* modult a kísérletvászonra  *[tanítási modell] [ train-model]*  modul, engedélyezi a tanítási kísérletet, amely a prediktív kísérletté használhatja új betanított modell létrehozásához. Ha rendelkezik egy *modell kiértékelése* modul, webes szolgáltatás kimeneti beolvasni értékelési eredmények kimenetként is csatolható.

A tanítási kísérletet frissítése:

1. Csatlakozás egy *Web Service bemeneti* az adatbevitel modul (például egy *Clean Missing Data* modul). Általában érdemes győződjön meg arról, hogy a bemeneti adatok feldolgozása a ugyanúgy, mint az eredeti betanítási adata.
2. Csatlakozás egy *webes szolgáltatás kimeneti* modul kimenetével a *tanítási modell* modul.
3. Ha rendelkezik egy *modell kiértékelése* modul és a kimeneti az értékelés eredményét, csatlakozni szeretne a *webes szolgáltatás kimeneti* modul kimenetével a *modell kiértékelése* modul.

Futtassa a kísérletet.

Ezután telepítenie kell a tanítási kísérletet egy webszolgáltatás, amely létrehozza a modell betanítását és modell kiértékelésének eredménye.  

A kísérlet vászon alján kattintson **webes szolgáltatások beállítása**, majd válassza ki **[Új] webes szolgáltatás telepítése**. Az Azure Machine Learning webszolgáltatások portal nyitja meg a **webes szolgáltatás telepítése** lap. Adjon meg egy nevet a webszolgáltatáshoz, fizetési csomag kiválasztása, és kattintson **telepítés**. Használhatja a kötegelt végrehajtási módszert csak a betanított modellek létrehozásához.

## <a name="retrain-the-model-with-new-data-by-using-bes"></a>A modell új adatokkal újratanítása BES használatával
Ehhez a példához használunk C# a megőrzési alkalmazás létrehozásához. Python vagy R mintakód segítségével ennek a feladatnak.

A megőrzési API-k meghívásához:

1. Hozzon létre egy C# konzolalkalmazást a Visual Studio: **új** > **projekt** > **Visual C#** > **klasszikus Windows asztal** > **Konzolalkalmazás (.NET-keretrendszer)**.
2. Jelentkezzen be a Machine Learning webszolgáltatások portálra.
3. Kattintson a webszolgáltatás, amelyet dolgozunk.
4. Kattintson a **felhasználásához**.
5. Alján a **felhasználás** lap a **mintakód** kattintson **kötegelt**.
6. A kötegelt végrehajtás C# mintakód másolja, majd illessze be a Program.cs fájlra. Győződjön meg arról, hogy a névtér érintetlen marad.

A megjegyzések Microsoft.AspNet.WebApi.Client, NuGet-csomag hozzáadása A hivatkozás Microsoft.WindowsAzure.Storage.dll hozzáadásához először meg kell telepíteni a [ügyféloldali kódtára a Azure Storage szolgáltatás](https://www.nuget.org/packages/WindowsAzure.Storage).

Az alábbi képernyőfelvételen látható a **felhasználás** oldal az Azure Machine Learning webszolgáltatások portálon.

![Lap felhasználása][1]

### <a name="update-the-apikey-declaration"></a>Frissítés a apikey nyilatkozat
Keresse meg a **apikey** deklarációjában:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Az a **alapvető fogyasztási adatai** szakasza a **felhasználás** lapon keresse meg az elsődleges kulcsot, és másolja a **apikey** nyilatkozatot.

### <a name="update-the-azure-storage-information"></a>Az Azure Storage-adatainak módosítása
A BES mintakód feltölt egy fájlt egy helyi meghajtó (például "C:\temp\CensusIpnput.csv") az Azure Storage, folyamatokat engedélyez, és kiírja az eredményeket vissza az Azure Storage.  

Az Azure Storage információk frissítéséhez be kell a tárfiók neve, a kulcs és a tároló adatokat olvasni a a tárfiók a klasszikus Azure portálon, és majd frissítési kísérletét, az eredményül kapott munkafolyamat futtatása után correspondi kell lennie a következőhöz hasonló:

![Eredményül kapott munkafolyamat futtatása után][4]a kód ng értékek.

1. Jelentkezzen be a klasszikus Azure portálra.
2. A bal oldali oszlopban kattintson **tárolási**.
3. Storage-fiókok a listában válassza ki a retrained modell tárolásához.
4. Kattintson a lap alján **elérési kulcsok kezelése**.
5. Másolja ki és mentse a **elsődleges elérési kulcsot** és a párbeszédpanel bezárásához.
6. Kattintson a lap tetején **tárolók**.
7. Egy meglévő tárolóhoz, válassza ki vagy hozzon létre egy újat, és mentse a nevét.

Keresse meg a *StorageAccountName*, *StorageAccountKey*, és *StorageContainerName* nyilatkozatok, és frissítse az értékeket a klasszikus portálon mentett.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Is gondoskodnia kell arról, hogy a bemeneti fájl érhető el a kódban a megadott helyre.

### <a name="specify-the-output-location"></a>Adja meg a kimeneti helyet
Ha a kimeneti helyet ad meg a kérelem hasznos, a megadott fájl kiterjesztése a *RelativeLocation* kell megadni, `ilearner`. Tekintse meg a következő példát:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Az alábbiakban egy példa megőrzési kimeneti: ![kimeneti Átképezési][6]

## <a name="evaluate-the-retraining-results"></a>Megőrzési eredmények értékelése
Az alkalmazás futtatásakor a parancs kimenete az URL-cím és a megosztott aláírások jogkivonatot az értékelés eredményét eléréséhez szükséges.

A teljesítmény eredmények retrained modell kombinálásával láthatók a *BaseLocation*, *RelativeLocation*, és *SasBlobToken* a kimeneti eredmények a *output2* (az előző megőrzési kimeneti ábrának megfelelően) és a teljes URL-cím beillesztése a böngésző címsorában.  

Vizsgálja meg az eredményeket a meghatározásához, hogy az újonnan betanított modell végez elégséges helyettesítse a meglévőt.

Másolás a *BaseLocation*, *RelativeLocation*, és *SasBlobToken* a kimeneti eredmények.

## <a name="retrain-the-web-service"></a>A webszolgáltatás újratanítása
Ha újratanítása egy új webszolgáltatás-bővítmény, frissítenie kell hivatkoznia, az új betanított modell a prediktív webszolgáltatás-definíciójának. A webszolgáltatás-definíciójának a betanított modell webszolgáltatás belső másolatát, és nincs közvetlen módosítható. Győződjön meg arról, hogy vannak-e a webszolgáltatás-definíciójának beolvasása a prediktív kísérletté és nem a tanítási kísérletet.

## <a name="sign-in-to-azure-resource-manager"></a>Jelentkezzen be az Azure Resource Manager számára
Ön először be kell jelentkeznie Azure-fiókjába a PowerShell környezetben használatával a [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) parancsmag.

## <a name="get-the-web-service-definition-object"></a>A webszolgáltatás-definíciójának objektum
A következő beolvasni a webszolgáltatás-definíciójának objektum meghívásával a [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) parancsmag.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Határozza meg az erőforráscsoport neve egy meglévő webszolgáltatás, futtassa a Get-AzureRmMlWebService parancsmagot a webes szolgáltatások megjelennek az előfizetés paraméter nélkül. Keresse meg a webes szolgáltatás, és tekintse meg a webes szolgáltatás azonosítóját. Az erőforráscsoport neve az azonosító, a negyedik eleme után a *resourceGroups* elemet. A következő példában az erőforráscsoport neve alapértelmezett-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Azt is megteheti az erőforráscsoport neve egy meglévő webszolgáltatás határozza meg, jelentkezzen be az Azure Machine Learning webszolgáltatások portálra. Válassza ki a webszolgáltatás. Az erőforráscsoport neve a webszolgáltatás URL-CÍMÉT a ötödik eleme után a *resourceGroups* elemet. A következő példában az erőforráscsoport neve alapértelmezett-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>A webszolgáltatás-definíciójának objektum exportálása JSON-fájlként
Az újonnan betanított modell használatára a betanított modell definíciója módosításához először használnia kell a [Export-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) parancsmag használatával exportálja a JSON-formátumú fájlba.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Frissítés a ilearner blobra mutató hivatkozás
Az eszközök, keresse meg a [betanított modell], frissítse a *uri* értéket a *locationInfo* ilearner BLOB URI-azonosítójú csomópont. Az URI egyesítésével létrejön a *BaseLocation* és a *RelativeLocation* a BES átképezési hívás a kimenetből.

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

## <a name="import-the-json-into-a-web-service-definition-object"></a>A JSON importálnia kell a webszolgáltatás-definíciójának objektum
Kell használnia a [Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) parancsmagot, hogy a módosított JSON-fájl átalakítása vissza egy webszolgáltatás-definíciójának objektum, amely segítségével a predicative kísérletben frissíteni.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>A webes szolgáltatás frissítése
Végül a [frissítés-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) parancsmaggal frissítsen a prediktív kísérletté.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

[1]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
