---
title: Klasszikus webszolgáltatás újratanítása és üzembe helyezése
titleSuffix: Azure Machine Learning Studio
description: Ismerje meg, hogyan modellek szoftveres átképezése, és frissítse a klasszikus webszolgáltatások használata az újonnan betanított modell az Azure Machine Learning Studióban.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: peterlu
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/14/2019
ms.openlocfilehash: 4f3ca01ae44900e4d0ce22b79db44d7bfa84e56d
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56456554"
---
# <a name="retrain-and-deploy-a-classic-studio-web-service"></a>Újratanítás PowerShell és a egy klasszikus Studio-webszolgáltatás üzembe helyezése

Machine learning-modellek átképezési módja egy tartása pontos és a legfontosabb rendelkezésre álló adatok alapján. Ebből a cikkből megtudhatja, hogyan Studio klasszikus webszolgáltatás újratanítása. Útmutató az új Studio webszolgáltatás újratanítása hogyan [a cikkben található útmutató megtekintéséhez.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy már rendelkezik egy megőrzési kísérletet és a egy prediktív kísérletet. Ezeket a lépéseket mutatjuk be [újratanítása és a egy gépi tanulási modell üzembe helyezése.](retrain-models-programmatically.md) Azonban helyett a gépi tanulási modell új webszolgáltatásként üzembe, telepíti a prediktív kísérletté klasszikus webszolgáltatásként.
     
## <a name="add-a-new-endpoint"></a>Új végpont hozzáadása

A prediktív webszolgáltatás üzembe helyezett tartalmaz egy alapértelmezett pontozási végpontjához, amely az eredeti képzési szinkronban maradjon, és kísérletek betanított modell pontozása. Frissítse a web service-t egy új betanított modell, létre kell hoznia egy új pontozási végpont.

Két módja, amelyben hozzáadhat egy új végpontot egy webszolgáltatás:

* Programozott módon
* Az Azure Web Services portál használata

> [!NOTE]
> Győződjön meg arról, hogy a végpont a prediktív webszolgáltatás, nem a betanítási webszolgáltatás ad hozzá. Ha megfelelően telepítette, egy tanítási és a egy prediktív webszolgáltatás, megtekintheti az felsorolt két külön webszolgáltatást. A prediktív webszolgáltatás "[prediktív exp.]" kell végződnie.
>

### <a name="programmatically-add-an-endpoint"></a>Programozott módon a végpont hozzáadása

Adhat hozzá azzal a mintakóddal ebben pontozási végpontok [GitHub-adattár](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Az Azure Web Services portál segítségével végpont hozzáadása

1. Machine Learning Studio a bal oldali navigációs oszlopban kattintson a Web Services.
1. A webszolgáltatás irányítópultján alján kattintson **kezelés végpontok előzetes**.
1. Kattintson a **Hozzáadás** parancsra.
1. Írjon be egy nevet és leírást az új végpont. Válassza ki a naplózási szint, és hogy engedélyezve van-e a mintaadatok. A naplózás további információkért lásd: [naplózás engedélyezése a Machine Learning-webszolgáltatások](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>A hozzáadott végpont betanított modell frissítése

### <a name="retrieve-patch-url"></a>JAVÍTÁSI URL-cím lekérése

### <a name="option-1-programmatically"></a>Option 1: Programozott módon

URL beolvasása a megfelelő JAVÍTÁSI programozott módon, kövesse az alábbi lépéseket:

1. Futtassa a [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) mintakódot.
1. Az AddEndpoint kimenetében található a *HelpLocation* értékét, és másolja az URL-címet.

   ![A kimenetben addEndpoint minta HelpLocation.](./media/troubleshooting-retraining-a-model/addEndpoint-output.png)
1. Illessze be az URL-címet egy böngészőben nyissa meg egy lapot, amely biztosítja a webszolgáltatás súgóhivatkozások.
1. Kattintson a **erőforrás frissítése** nyissa meg a patch Súgó hivatkozásra.

### <a name="option-2-use-the-azure-machine-learning-web-services-portal"></a>Option 2: Az Azure Machine Learning Web Services portál használata

Kövesse az alábbi lépéseket a megfelelő JAVÍTÁSI URL-Címének lekéréséhez a webes portál használatával:

1. Jelentkezzen be a [Azure Machine Learning webszolgáltatások](https://services.azureml.net/) portálon.
1. Kattintson a **webszolgáltatások** vagy **klasszikus webszolgáltatások** tetején.
1. Kattintson a pontozási webszolgáltatás használata (Ha nem módosítja az alapértelmezett nevet, a webszolgáltatás, azt megszűnik az "[pontozás Exp.]").
1. Kattintson a **+ új**.
1. A végpont hozzáadása után kattintson a végpont neve.
1. Alatt a **javítás** URL-CÍMÉT, kattintson a **API Help** nyissa meg a javítási segítséget.

> [!NOTE]
> Ha a végpont a betanítási Web Service helyett a prediktív webszolgáltatás adott, a következő hibaüzenetet kap, kattintva a **erőforrás frissítése** hivatkozásra: "Sajnáljuk, de ez a funkció nem támogatott vagy nem érhető el ebben a környezetben. A webszolgáltatás nem rendelkezik nem frissíthető erőforrásokkal. Hogy kérünk az okozott kellemetlenségekért, és dolgozunk azon, hogy ez a munkafolyamat javítására."
>

A PATCH súgóoldalt tartalmazza, a JAVÍTÁSI URL-címet kell használnia, és segítségével meghívására mintakódot biztosít.

![Javítási URL-címe.](./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>A végpont frissítéséhez

Mostantól használhatja a betanított modell a pontozási végpont korábban létrehozott frissítéséhez.

Az alábbi mintakód bemutatja, hogyan használható a *BaseLocation*, *RelativeLocation*, *SasBlobToken*, és URL-cím JAVÍTANI a végpont frissítéséhez.

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

A *apikey tulajdonsággal végzett tesztelése* és a *endpointUrl* az a hívás végpontja irányítópult szerezhető.

Értékét a *neve* paraméter *erőforrások* meg kell egyeznie a mentett, a prediktív kísérletet a betanított modell erőforrás nevét. Az erőforrásnév lekérése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali menüben kattintson a **Machine Learning**.
1. A név, kattintson a munkaterület, és kattintson a **webszolgáltatások**.
1. A név, kattintson a **népszámlálási modell [prediktív exp.]** .
1. Kattintson a hozzáadott új végpont.
1. A végpont irányítópultján kattintson **erőforrás frissítése**.
1. A web service erőforrás-frissítési API-dokumentáció lapon talál a **erőforrásnév** alatt **frissíthető erőforrások**.

Ha lejár az SAS-jogkivonatot, mielőtt áttelepítené a végpont frissítése, egy GET friss jogkivonatot beszereznie a feladatazonosító kell végrehajtania.

Ha a kódot már sikeresen futott, az új végpont el kell a retrained modell használatával körülbelül 30 másodperc múlva.

## <a name="next-steps"></a>További lépések

Többet webszolgáltatások kezelése, vagy nyomon követheti, több kísérletek Futtatás kapcsolatban, tekintse meg a következő cikkeket:

* [A Web Services portál felfedezése](manage-new-webservice.md)
* [Kísérletismétlések kezelése](manage-experiment-iterations.md)