---
title: Klasszikus webszolgáltatás újratanítása
titleSuffix: ML Studio (classic) - Azure
description: Ismerje meg, hogyan lehet újrabetanítása egy modellt, és frissíti a klasszikus webszolgáltatás az újonnan betanított modell az Azure Machine Learning Studio (klasszikus).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: peterclu
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/14/2019
ms.openlocfilehash: 8094d64eab1a4b25a76554bf9eb6848c2e4d3493
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204239"
---
# <a name="retrain-and-deploy-a-classic-studio-classic-web-service"></a>Klasszikus Studio (klasszikus) webszolgáltatás újratanítása és üzembe helyezése

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

A gépi tanulási modellek átképzése az egyik módja annak, hogy pontosak maradjanak, és a rendelkezésre álló legrelevánsabb adatok alapján maradjanak. Ez a cikk bemutatja, hogyan lehet újrabetanítása a klasszikus Studio (klasszikus) webszolgáltatás. Egy útmutató, hogyan kell újrabetanítása egy új Studio (klasszikus) webszolgáltatás, [tekintse meg ezt az útmutató cikket.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy már rendelkezik átképzési és prediktív kísérlettel is. Ezeket a lépéseket az újratanítás és [a gépi tanulási modell üzembe helyezése ismerteti.](/azure/machine-learning/studio/retrain-machine-learning-model) Azonban ahelyett, hogy a gépi tanulási modell egy új webszolgáltatásként, üzembe helyezi a prediktív kísérlet klasszikus webszolgáltatásként.
     
## <a name="add-a-new-endpoint"></a>Új végpont hozzáadása

A prediktív webszolgáltatás, amely üzembe helyezett tartalmaz egy alapértelmezett pontozási végpontot, amely szinkronban van az eredeti betanítási és pontozási kísérletek betanított modell. A webszolgáltatás frissítése egy új betanított modell, létre kell hoznia egy új pontozási végpontot.

A webszolgáltatáshoz kétféleképpen adhat hozzá új végpontot:

* Programozott módon
* Az Azure Web Services portál használata

> [!NOTE]
> Győződjön meg arról, hogy a végpontot a prediktív webszolgáltatáshoz adja hozzá, nem pedig a betanítási webszolgáltatáshoz. Ha a képzési és a prediktív webszolgáltatás is helyesen telepítette a programot, két külön webszolgáltatást kell látnia. A prediktív webszolgáltatásnak "[prediktív exp.]" végződéssel kell végződnie.
>

### <a name="programmatically-add-an-endpoint"></a>Programozott módon végpont hozzáadása

A pontozó végpontokat az ebben a [GitHub-tárházban](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint)megadott mintakód használatával adhat hozzá.

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Végpont hozzáadása az Azure Web Services portálon

1. A Machine Learning Studio (klasszikus) alkalmazásban a bal oldali navigációs oszlopban kattintson a WebServices elemre.
1. A webszolgáltatás irányítópultjának alján kattintson a **Végpontok előnézetének kezelése gombra.**
1. Kattintson a **Hozzáadás** gombra.
1. Írja be az új végpont nevét és leírását. Válassza ki a naplózási szintet, és hogy a mintaadatok engedélyezve vannak-e. A naplózásról a [Machine Learning webszolgáltatások naplózásának engedélyezése](web-services-logging.md)című témakörben talál további információt.

## <a name="update-the-added-endpoints-trained-model"></a>A hozzáadott végpont betanított modelljének frissítése

### <a name="retrieve-patch-url"></a>A JAVÍTÁS URL-címének beolvasása

A megfelelő PATCH URL-cím beszerezéséhez kövesse az alábbi lépéseket a webportálhasználatával:

1. Jelentkezzen be az [Azure Machine Learning webszolgáltatási](https://services.azureml.net/) portálra.
1. Felül kattintson a **Webservices** vagy a **Klasszikus webszolgáltatások** elemre.
1. Kattintson arra a pontozási webszolgáltatásra, amelyen dolgozik (ha nem módosította a webszolgáltatás alapértelmezett nevét, az "[Pontozási exp.]") végződik.
1. Kattintson **a +ÚJ gombra.**
1. A végpont hozzáadása után kattintson a végpont nevére.
1. A **Javítás** URL-címe alatt kattintson az **API Súgó gombjára** a javítási súgólap megnyitásához.

> [!NOTE]
> Ha a végpontot a prediktív webszolgáltatás helyett a betanítási webszolgáltatáshoz adta hozzá, a következő hibaüzenet jelenik meg, amikor az **Erőforrás frissítése** hivatkozásra kattint: "Sajnáljuk, de ez a szolgáltatás nem támogatott vagy nem érhető el ebben a környezetben. Ez a webszolgáltatás nem rendelkezik javítható erőforrásokkal. Elnézést kérünk a kellemetlenségért, és dolgozunk a munkafolyamat javításán."
>

A PATCH súgóoldala tartalmazza a JAVÍTÁS URL-címét, amelyet használnia kell, és mintakódot biztosít, amelyet a híváshoz használhat.

![Patch URL-címe.](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>A végpont frissítése

Most már használhatja a betanított modell a korábban létrehozott pontozási végpont frissítéséhez.

A következő mintakód bemutatja, hogyan használhatja a *BaseLocation*, *RelativeLocation*, *SasBlobToken*és PATCH URL-cím a végpont frissítéséhez.

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
                        RelativeLocation = "your endpoint relative location", //from the output, for example: "experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner"
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: "?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl"
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

Az *apiKey* és a *végpontUrl* a hívás beszerezhető a végpont irányítópultján.

Az *erőforrások* *név* paraméterének meg kell egyeznie a prediktív kísérletben a mentett betanított modell erőforrásnevével. Az erőforrás nevének beszerezése:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A bal oldali menüben kattintson a **Gépi tanulás parancsra.**
1. A Név csoportban kattintson a munkaterületre, majd a **Webservices**gombra.
1. A Név csoportban kattintson **a Népszámlálási modell [prediktív exp.]** elemre.
1. Kattintson a hozzáadott új végpontra.
1. A végpont irányítópultján kattintson az **Erőforrás frissítése**gombra.
1. A webszolgáltatás Erőforrás API-dokumentációfrissítése lapján az **Erőforrás neve** a **Frissíthető erőforrások területen**található.

Ha a SAS-jogkivonat lejár, mielőtt befejeze a végpont frissítése, végre kell hajtania egy GET a feladatazonosítót, hogy egy friss jogkivonatot szerezzen be.

Ha a kód sikeresen futott, az új végpontnak körülbelül 30 másodpercen belül meg kell kezdenie az újrabetanított modell használatát.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a rról, hogyan kezelheti a webszolgáltatásokat, illetve hogyan követheti nyomon a több kísérletfuttatást, olvassa el az alábbi cikkeket:

* [A webszolgáltatások portál jának felfedezése](manage-new-webservice.md)
* [Kísérletismétlések kezelése](manage-experiment-iterations.md)