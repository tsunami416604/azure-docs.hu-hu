---
Cím: Újratanítás PowerShell egy klasszikus web service titleSuffix: Azure Machine Learning Studio description: Ismerje meg, hogyan programozott módon modellek szoftveres átképezése és frissíteni a webszolgáltatást a újonnan betanított modell használata az Azure Machine Learning.
szolgáltatások: gépi tanulási ms.service: gépi tanulási ms.component: studio ms.topic: cikk

author: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT ms.date: 04/19/2017
---
# <a name="retrain-a-classic-azure-machine-learning-studio-web-service"></a>A klasszikus Azure Machine Learning Studio webszolgáltatás újratanítása
A prediktív webszolgáltatás üzembe helyezte az alapértelmezett érték pontozási végpontjához. Alapértelmezett végpontok szinkronban vannak az eredeti tanítási és pontozási kísérletek őrzi meg, és ezért a betanított modell esetében az alapértelmezett végpont nem lehet lecserélni. A webszolgáltatás újratanítása, hozzá kell adnia egy új végpont a webszolgáltatást.

## <a name="prerequisites"></a>Előfeltételek
Akkor be kell állítania egy tanítási kísérletet, és a egy prediktív kísérletet, ahogyan [Retrain Machine Learning-modellek](retrain-models-programmatically.md).

> [!IMPORTANT]
> A prediktív kísérletté klasszikus machine learning-webszolgáltatás, telepíteni kell.
>
>

Webszolgáltatások üzembe helyezése a további információkért lásd: [egy Azure Machine Learning webszolgáltatás üzembe helyezése](publish-a-machine-learning-web-service.md).

## <a name="add-a-new-endpoint"></a>Új végpont hozzáadása
A prediktív webszolgáltatás üzembe helyezett tartalmaz egy alapértelmezett pontozási végpontjához, amely az eredeti képzési szinkronban maradjon, és kísérletek betanított modell pontozása. Frissítse a web service-t egy új betanított modell, létre kell hoznia egy új pontozási végpont.

Új pontozási végpont, az a prediktív webszolgáltatás, amely frissíthetők a betanított modell létrehozásához:

> [!NOTE]
> Győződjön meg arról, hogy a végpont a prediktív webszolgáltatás, nem a betanítási webszolgáltatás ad hozzá. Ha megfelelően telepítette, egy tanítási és a egy prediktív webszolgáltatás, megtekintheti az felsorolt két külön webszolgáltatást. A prediktív webszolgáltatás "[prediktív exp.]" kell végződnie.
>
>

Két módja, amelyben hozzáadhat egy új végpontot egy webszolgáltatás:

1. Programozott módon
2. A Microsoft Azure Web Services portál használata

### <a name="programmatically-add-an-endpoint"></a>Programozott módon a végpont hozzáadása
Adhat hozzá azzal a mintakóddal ebben pontozási végpontok [GitHub-adattár](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-microsoft-azure-web-services-portal-to-add-an-endpoint"></a>Végpont hozzáadása a Microsoft Azure Web Services portál használata
1. Machine Learning Studio a bal oldali navigációs oszlopban kattintson a Web Services.
2. A webszolgáltatás irányítópultján alján kattintson **kezelés végpontok előzetes**.
3. Kattintson a **Hozzáadás** parancsra.
4. Írjon be egy nevet és leírást az új végpont. Válassza ki a naplózási szint, és hogy engedélyezve van-e a mintaadatok. A naplózás további információkért lásd: [naplózás engedélyezése a Machine Learning-webszolgáltatások](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>A hozzáadott végpont betanított modell frissítése
A megőrzési befejezéséhez, frissítenie kell a hozzáadott új végpont a betanított modell.

Ha a végpont, azzal a mintakóddal, ez magában foglalja a súgó által azonosított URL-cím helyét a *HelpLocationURL* a kimenetben szereplő érték.

Az elérési út URL-cím lekéréséhez:

1. Másolja és illessze be az URL-címet a böngészőben.
2. Kattintson a frissítés erőforrás-hivatkozás.
3. Másolja a POST URL-címét a PATCH-kérés. Példa:
   
     JAVÍTÁSI URL-CÍME: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

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
2. A bal oldali menüben kattintson a **Machine Learning**.
3. A név, kattintson a munkaterület, és kattintson a **webszolgáltatások**.
4. A név, kattintson a **népszámlálási modell [prediktív exp.]** .
5. Kattintson a hozzáadott új végpont.
6. A végpont irányítópultján kattintson **erőforrás frissítése**.
7. A web service erőforrás-frissítési API-dokumentáció lapon talál a **erőforrásnév** alatt **frissíthető erőforrások**.

Ha lejár az SAS-jogkivonatot, mielőtt áttelepítené a végpont frissítése, egy GET friss jogkivonatot beszereznie a feladatazonosító kell végrehajtania.

Ha a kódot már sikeresen futott, az új végpont el kell a retrained modell használatával körülbelül 30 másodperc múlva.

## <a name="summary"></a>Összegzés
Az Átképezési API-k használatával, frissítheti a betanított modell egy prediktív webszolgáltatás például forgatókönyvek engedélyezése:

* Rendszeres modell új adatokkal átképezési.
* A modell az ügyfelek számára a cél az, amely tájékoztatja őket a modell használatával saját adataik újratanítás terjesztési.

## <a name="next-steps"></a>További lépések
[A klasszikus Azure Machine Learning webszolgáltatás átképezési hibaelhárítása](troubleshooting-retraining-models.md)

