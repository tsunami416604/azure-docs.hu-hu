---
title: A klasszikus webszolgáltatás újratanítása |} Microsoft Docs
description: Megtudhatja, hogyan programozott módon modell működik, és frissíti a az újonnan betanított modell használatára az Azure Machine Learning webszolgáltatás.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: e36e1961-9e8b-4801-80ef-46d80b140452
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 6fc03865185b97fb1f34028239f647f97d5bd315
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836874"
---
# <a name="retrain-a-classic-web-service"></a>Klasszikus webszolgáltatás újratanítása
A prediktív telepített webes szolgáltatás az alapértelmezett érték scoring-végpontja. Alapértelmezett végpontok szinkronban vannak az eredeti képzési és kísérletek pontozási tárolják, és ezért a betanított modell alapértelmezett végpont nem cserélhető le. A webszolgáltatás működik, hozzá kell adnia egy új végpont a webszolgáltatással. 

## <a name="prerequisites"></a>Előfeltételek
Akkor be kell állítania egy tanítási kísérletet, és egy prediktív kísérletté látható módon [Machine Learning-modellek szoftveres](retrain-models-programmatically.md). 

> [!IMPORTANT]
> A prediktív kísérletté kell telepíteni, mint a klasszikus machine learning-webszolgáltatás. 
> 
> 

A web Services további információkért lásd: [központi telepítése az Azure Machine Learning webszolgáltatás](publish-a-machine-learning-web-service.md).

## <a name="add-a-new-endpoint"></a>Új végpont hozzáadása
A prediktív webszolgáltatás központilag telepített tartalmaz egy alapértelmezett scoring-végpontja, hogy szinkronban vannak az eredeti képzési és kísérletek betanított modell pontozása. A webszolgáltatás számára új betanított modell egy új pontozási végpontot kell létrehoznia. 

Egy új pontozási végpont létrehozásához a betanított modell frissíthető prediktív webszolgáltatás:

> [!NOTE]
> Győződjön meg arról, hogy a végpont a prediktív webszolgáltatás, nem a képzés webszolgáltatás kíván hozzáadni. Ha megfelelően telepítette, a képzési és a prediktív webszolgáltatás, megtekintheti az felsorolt két külön webszolgáltatások. A prediktív webszolgáltatás "[prediktív exp.]" kell végződnie.
> 
> 

Kétféleképpen, amelyben hozzáadhat egy új végpont egy webszolgáltatás-bővítmény:

1. Programozott módon
2. A Microsoft Azure Web Services portál

### <a name="programmatically-add-an-endpoint"></a>Programozott módon a végpont hozzáadása
Pontozó végpontjaitól jelen mintakód is hozzáadhat [github-tárházban](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).

### <a name="use-the-microsoft-azure-web-services-portal-to-add-an-endpoint"></a>Végpont hozzáadása a Microsoft Azure Web Services portál használatával
1. A Machine Learning Studio a bal oldali oszlopban kattintson a webszolgáltatásokat.
2. A webes szolgáltatás irányítópultját alján kattintson **kezelése végpontok előzetes**.
3. Kattintson a **Hozzáadás** parancsra.
4. Írja be nevét és leírását, az új végpont. Válassza ki a naplózási szint, és hogy engedélyezve van-e mintaadatokat. További információt a naplózást, [naplózását a Machine Learning webszolgáltatások](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>A hozzáadott végpont betanított modell frissítése
A megőrzési befejezéséhez, frissítenie kell a hozzáadott új végpont a betanított modell.

Ha hozzáadta a végpont a mintakódot, ez magában foglalja a súgó által azonosított URL-cím helye a *HelpLocationURL* a kimeneti értéket.

Az útvonal URL-cím beolvasása:

1. Másolja és illessze be az URL-CÍMÉT a böngésző címsorába.
2. A frissítés erőforrás hivatkozásra.
3. Másolja a PATCH kérés POST URL-CÍMÉT. Példa:
   
     JAVÍTÁS URL-CÍME: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

Most már használhatja a betanított modell a korábban létrehozott pontozási végpont frissítéséhez.

Az alábbi mintakód bemutatja, hogyan használható a *BaseLocation*, *RelativeLocation*, *SasBlobToken*, és az URL-cím javítására, de a végpont frissítéséhez.

    private async Task OverwriteModel()
    {
        var resourceLocations = new
        {
            Resources = new[]
            {
                new
                {
                    Name = "Census Model [trained model]",
                    Location = new AzureBlobDataReference()
                    {
                        BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                        RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
                    }
                }
            }
        };

        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

            using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
            {
                request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
                HttpResponseMessage response = await client.SendAsync(request);

                if (!response.IsSuccessStatusCode)
                {
                    await WriteFailedResponse(response);
                }

                // Do what you want with a successful response here.
            }
        }
    }

A *apiKey* és a *végponti URL-cím* vonatkozó hívása végpont irányítópult lehet lekérni.

Értékét a *neve* paraméterének *erőforrások* meg kell felelnie a prediktív kísérletté a mentett betanított modell erőforrás neve. Az erőforrásnév beolvasása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben kattintson **Machine Learning**.
3. A név, kattintson a munkaterület majd **webszolgáltatások**.
4. A név, kattintson a **nyilvántartásba modell [prediktív exp].** .
5. Kattintson a hozzáadott új végpontot.
6. A végpont irányítópultján kattintson **frissítés erőforrás**.
7. A webszolgáltatás frissítés erőforrás API-JÁNAK dokumentációja lapon található a **erőforrásnév** alatt **frissíthető erőforrások**.

Ha befejezte a végpont frissítése a SAS-jogkivonat nem, el kell végeznie egy GET friss jogkivonat beszerzése feladatazonosítóval.

Amikor a kód sikeresen lefutott, az új végpont kell kezdődnie, körülbelül 30 másodpercet retrained modellt használja.

## <a name="summary"></a>Összegzés
Az Átképezési API-kkal, frissítheti a betanított modell egy prediktív webszolgáltatás forgatókönyvek például engedélyezése:

* Az új adatokat átképezési rendszeres modell.
* Terjesztési ügyfelek-modell, azzal a céllal, hogy működik a modell használatával saját adataikat.

## <a name="next-steps"></a>További lépések
[A klasszikus Azure Machine Learning webszolgáltatás átképezési hibaelhárítása](troubleshooting-retraining-models.md)

