---
title: Webszolgáltatások kezelése API Management használatával
titleSuffix: ML Studio (classic) - Azure
description: Az API Management használatával AzureML webszolgáltatások kezelése bemutató útmutató. A REST API-végpontokon kezelheti a felhasználói hozzáférés-szabályozás és figyelési irányítópult definiálásával.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: cbe01ee9b8edeab349db484cea6c25dca32bf213
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218028"
---
# <a name="manage-azure-machine-learning-studio-classic-web-services-using-api-management"></a>Azure Machine Learning Studio (klasszikus) webszolgáltatások kezelése API Management használatával

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan lehet gyorsan megkezdeni a API Management használatát a Azure Machine Learning Studio (klasszikus) webszolgáltatások kezeléséhez.

## <a name="what-is-azure-api-management"></a>Mi az Azure API Management?
Az Azure API Management egy Azure-szolgáltatás, amellyel a REST API-végpontokat kezelheti a felhasználói hozzáférés-szabályozás és figyelési irányítópult definiálásával. További részletekért tekintse meg az [Azure API Management webhelyét](https://azure.microsoft.com/services/api-management/) . Az Azure API Management megkezdéséhez tekintse meg [az importálási és közzétételi útmutatót](/azure/api-management/import-and-publish). A többi útmutató, amely a jelen útmutató alapján, termékek, a fejlesztői előfizetésekhez és a használati dashboarding további témaköreit, beleértve az értesítési konfiguráció, szint díjszabása, válasz kezelését, felhasználói hitelesítés, ismerteti.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató végrehajtásához lesz szüksége:

* Egy Azure-fiók.
* Az AzureML-fiók.
* A munkaterület, a szolgáltatás és a egy webszolgáltatásként üzembe helyezett AzureML kísérlethez api_key. A AzureML-kísérletek létrehozásával kapcsolatos részletekért tekintse meg a [Studio](create-experiment.md)rövid útmutatóját. A studiók (klasszikus) webszolgáltatásként történő üzembe helyezésével kapcsolatos információkért tekintse meg a [Studio üzembe helyezési útmutatója](deploy-a-machine-learning-web-service.md) című témakört, amely azt ismerteti, hogyan lehet webszolgáltatásként üzembe helyezni egy AzureML-kísérletet. A függelék rendelkezik, azt is megteheti, létrehozása és tesztelése egy egyszerű AzureML kísérletet, és helyezze üzembe webszolgáltatásként, amely utasításokat.

## <a name="create-an-api-management-instance"></a>API Management-példány létrehozása

Az API Management-példány az Azure Machine Learning webszolgáltatás kezelheti.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
2. Válassza a **+ Erőforrás létrehozása** lehetőséget.
3. A Keresés mezőbe írja be az "API-kezelési", majd válassza az "API-kezelés" erőforráshoz.
4. Kattintson a  **Create** (Létrehozás) gombra.
5. A **Name** érték egy egyedi URL-cím létrehozására szolgál (ez a példa a "demoazureml" kifejezést használja).
6. Válasszon egy **előfizetést**, **erőforráscsoportot**és **helyet** a szolgáltatási példány számára.
7. Adja meg a **szervezet nevét** (ez a példa a "demoazureml" értéket használja).
8. Adja meg a **rendszergazda e-mail** -címét – ezt az e-mailt fogja használni a API Management rendszer értesítéseihez.
9. Kattintson a  **Create** (Létrehozás) gombra.

Létrejön egy új szolgáltatáshoz akár 30 percig is eltarthat.

![szolgáltatás létrehozása](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Az API létrehozása
A szolgáltatáspéldány létrehozása után a a következő lépés, ha az API-t. Az API egy ügyfélalkalmazásokból meghívható műveletkészletből áll. Az API-műveleteket létező webszolgáltatásokhoz használják proxyként. Ez az útmutató a meglévő AzureML RRS- és BES-webszolgáltatásokat a proxyk API-kat hoz létre.

Az API létrehozásához:

1. A Azure Portal nyissa meg a létrehozott szolgáltatási példányt.
2. A bal oldali navigációs panelen válassza az **API**-k elemet.

   ![api-management-menu](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Kattintson az **API hozzáadása**lehetőségre.
2. Adja meg a **webes API nevét** (ez a példa a "AZUREML demo API"-t használja).
3. A **webszolgáltatás URL-címe**mezőbe írja be a "`https://ussouthcentral.services.azureml.net`" értéket.
4. Adja meg a ** webes API URL-Címének utótagja ". Ez lesz az ügyfelek által használt, a kérések küldését a szolgáltatáspéldány (ebben a példában használja "azureml-demo") URL-cím utolsó része.
5. A **webes API URL-sémája**beállításnál válassza a **https**lehetőséget.
6. **Termékek**esetében válassza az **alapszintű**lehetőséget.
7. Kattintson a **Save** (Mentés) gombra.


## <a name="add-the-operations"></a>A műveletek hozzáadása

Műveletek hozzáadása és API-khoz, a közzétevő portálon konfigurált. A közzétevő portál eléréséhez kattintson a **közzétevő portál** elemre az API Management szolgáltatás Azure Portaljában, majd válassza az **API**-k, **műveletek**, majd a **művelet hozzáadása**lehetőséget.

![művelet hozzáadása](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

Ekkor megjelenik az **új művelet** ablak, és alapértelmezés szerint az **aláírás** lap lesz kiválasztva.

## <a name="add-rrs-operation"></a>RRS művelet hozzáadása
Először hozzon létre egy műveletet az AzureML RRS szolgáltatás:

1. A **http-művelethez**válassza a **post**lehetőséget.
2. Az **URL-sablonnál**írja be a következőt: "`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`".
3. Adja meg a **megjelenítendő nevet** (ez a példa a "erőforrásrekordok végrehajtása" kifejezést használja).

   ![Adjon hozzá-rrs-művelet-aláírás](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Kattintson a **válaszok** > **Hozzáadás** a bal oldalon, majd válassza a **200 OK**lehetőséget.
5. A művelet mentéséhez kattintson a **Mentés** gombra.

   ![Adjon hozzá-rrs-művelet-válasz](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>BES műveletek hozzáadása

> [!NOTE]
> Képernyőképek nem szerepelnek itt a BES műveletek azok nagyon hasonlóak az RRS művelet hozzáadásához.

### <a name="submit-but-not-start-a-batch-execution-job"></a>A kötegelt végrehajtási feladat elküldése (de nem indul el)

1. Kattintson a **művelet hozzáadása** lehetőségre egy BES művelet az API-hoz való hozzáadásához.
2. A **http-művelethez**válassza a **post**lehetőséget.
3. Az **URL-sablonnál**írja be a következőt: "`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`".
4. Adja meg a **megjelenítendő nevet** (ez a példa a "BES submit" kifejezést használja).
5. Kattintson a **válaszok** > **Hozzáadás** a bal oldalon, majd válassza a **200 OK**lehetőséget.
6. Kattintson a **Save** (Mentés) gombra.

### <a name="start-a-batch-execution-job"></a>Kötegelt végrehajtási feladat indítása

1. Kattintson a **művelet hozzáadása** lehetőségre egy BES művelet az API-hoz való hozzáadásához.
2. A **http-művelethez**válassza a **post**lehetőséget.
3. A **http-művelethez**írja be a következőt: "`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`".
4. Adja meg a **megjelenítendő nevet** (ez a példa a "BES Start" kifejezést használja).
6. Kattintson a **válaszok** > **Hozzáadás** a bal oldalon, majd válassza a **200 OK**lehetőséget.
7. Kattintson a **Save** (Mentés) gombra.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Az állapot vagy a kötegelt végrehajtási feladat eredménye

1. Kattintson a **művelet hozzáadása** lehetőségre egy BES művelet az API-hoz való hozzáadásához.
2. A **http-művelethez**válassza a **beolvasás**lehetőséget.
3. Az **URL-sablonnál**írja be a következőt: "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Adja meg a **megjelenítendő nevet** (ez a példa a "BES status" kifejezést használja).
6. Kattintson a **válaszok** > **Hozzáadás** a bal oldalon, majd válassza a **200 OK**lehetőséget.
7. Kattintson a **Save** (Mentés) gombra.

### <a name="delete-a-batch-execution-job"></a>Kötegelt végrehajtási feladat törlése

1. Kattintson a **művelet hozzáadása** lehetőségre egy BES művelet az API-hoz való hozzáadásához.
2. A **http-művelethez**válassza a **Törlés**lehetőséget.
3. Az **URL-sablonnál**írja be a következőt: "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Adja meg a **megjelenítendő nevet** (ez a példa a "BES Delete" kifejezést használja).
5. Kattintson a **válaszok** > **Hozzáadás** a bal oldalon, majd válassza a **200 OK**lehetőséget.
6. Kattintson a **Save** (Mentés) gombra.

## <a name="call-an-operation-from-the-developer-portal"></a>Művelet meghívása a fejlesztői portálról

Közvetlenül a fejlesztői portál, amely megtekintésére és tesztelésére egy API műveleteit kényelmes módot biztosít a lehet hívni. Ebben a lépésben a **AzureML bemutató API**-hoz hozzáadott **erőforrásrekordok-végrehajtási** metódust kell meghívni. 

1. Kattintson a **fejlesztői portál**elemre.

   ![fejlesztői – portál](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Kattintson az **API** -k elemre a felső menüben, majd kattintson a **AzureML demo API** elemre az elérhető műveletek megtekintéséhez.

   ![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Válassza ki a művelethez tartozó **erőforrásrekordok-végrehajtás** elemet. Kattintson a **kipróbálás**gombra.

   ![try-it](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. A **kérés paramétereinek**megtekintéséhez írja be a **munkaterületet** és a **szolgáltatást**, írja be a "2,0 for the **apiversion**" és a "true" értéket a **részletekre**. A **munkaterület** és a **szolgáltatás** a AzureML webszolgáltatás irányítópultján található (lásd: **a webszolgáltatás tesztelése** az a függelékben).

   A **kérések fejléce**lapon kattintson a **fejléc hozzáadása** elemre, és írja be a "Content-Type" és az "Application/JSON" kifejezést. Kattintson ismét a **fejléc hozzáadása** elemre, és írja be az "engedélyezés" és a "tulajdonos *\<a Service API-kulcs\>* " értéket. Az API-kulcs a AzureML webszolgáltatás irányítópultján található (lásd: **a webszolgáltatás tesztelése** az a függelékben).

   A **kérelem törzse**mezőbe írja be a következőt: `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`.

   ![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Kattintson a **Küldés**gombra.

   ![Küldés](./media/manage-web-service-endpoints-using-api-management/send.png)

A művelet meghívása után a fejlesztői portál megjeleníti a **kért URL-címet** a háttér-szolgáltatástól, a **Válasz állapotától**, a válasz **fejléctől**és a **Válasz tartalmától**.

![Válasz-állapota](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>A függelék – létrehozása és tesztelése egy egyszerű AzureML webszolgáltatás
### <a name="creating-the-experiment"></a>A kísérlet létrehozása
Az alábbiakban az AzureML egyszerű kísérlet létrehozására és központi telepítésére, webszolgáltatásként lépéseket. A web service veszi adjon meg egy tetszőleges szöveges oszlop és funkciókat egészként képviselt készletét adja vissza. Például:

| Szöveg | Kivonatolt szöveg |
| --- | --- |
| Ez a jó naponta |1 1 2 2 0 2 0 1 |

Először a kívánt böngésző használatával lépjen a következő helyre: [https://studio.azureml.net/](https://studio.azureml.net/) , majd adja meg a bejelentkezéshez szükséges hitelesítő adatokat. Ezután hozzon létre egy új üres kísérlet.

![a sablonok kísérlet keresése](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Nevezze át a **SimpleFeatureHashingExperiment**. Bontsa ki a **mentett adatkészletek** csomópontot, és húzza a **Book Reviews from Amazon elemet** a kísérletre.

![egyszerű – a szolgáltatás-kivonatolás – kísérlet](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Bontsa ki az **adatátalakítás** és- **kezelés** elemet, és húzza az **adatkészletben az Oszlopok kiválasztása lehetőséget** a kísérletbe. A **Book-értékelések** összekapcsolhatók az Amazontól az **adatkészlet oszlopainak kiválasztásához**.

![A Book Reviews DataSet modul csatlakoztatása egy Project Columns modulhoz](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Kattintson **az Oszlopok kiválasztása az adatkészletben** elemre, majd kattintson az **oszlop indítása** elemre, és válassza a **Col2**lehetőséget. Kattintson a pipa jelre a módosítások alkalmazásához.

![Oszlopok kijelölése oszlopnevek használatával](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Bontsa ki **text Analytics** és húzza a **szolgáltatás-kivonatolást** a kísérletre. Az **adatkészlethez tartozó oszlopok kiválasztása** a **szolgáltatások kivonatolásához**.

![Connect-projekt-oszlopok](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Írja be a **3** értéket a **kivonatoló bitsize**. Ekkor létrejön a 8 (23) oszlopot.

![kivonatoló bitsize](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

Ezen a ponton érdemes a **Futtatás** gombra kattintani a kísérlet teszteléséhez.

![Futtatás](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Webszolgáltatás létrehozása
Most hozzon létre egy webes szolgáltatás. Bontsa ki a **webszolgáltatás** csomópontot, és húzza a **bemenetet** a kísérletbe. A **bemenet** összekötése a **szolgáltatások kivonatolásával**. Húzza a **kimenetet** a kísérletbe is. A **kimenet** összekötése a **szolgáltatások kivonatolásával**.

![kimeneti-az--szolgáltatáskivonatolás](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Kattintson a **webszolgáltatások közzététele**elemre.

![publish-web-service](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

A kísérlet közzétételéhez kattintson az **Igen** gombra.

![Igen – közzététel](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>A webszolgáltatás teszteléséhez
Az AzureML webszolgáltatás (kérelem/válasz szolgáltatás) az RSS- és BES (kötegelt végrehajtási szolgáltatás) végpontok áll. Az RSS szinkron végrehajtásra van. BES az aszinkron feladatok végrehajtásra. Ha a webszolgáltatást az alábbi példa Python-forrással szeretné tesztelni, előfordulhat, hogy le kell töltenie és telepítenie kell a Pythonhoz készült Azure SDK-t (lásd: a [Python telepítése](/azure/python/python-sdk-azure-install)).

Szüksége lesz az alábbi mintakód **munkaterületére**, **szolgáltatására**és **api_keyére** is. A munkaterületet és a szolgáltatást úgy keresheti meg, hogy a kísérletet a webszolgáltatás irányítópultján a **kérelem/válasz** vagy a **kötegelt végrehajtás** lehetőségre kattint.

![find-workspace-and-service](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

A **api_key** a webszolgáltatás irányítópultján a kísérletre kattintva érheti el.

![Keresés – api-kulcs](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Teszt RRS-végpont
##### <a name="test-button"></a>Teszt gomb
Az RR-végpont tesztelésének egyszerű módja, ha a **tesztelés** gombra kattint a webszolgáltatás irányítópultján.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

Írja be **, hogy ez egy jó nap** a **col2**. Kattintson a pipa jelre.

![Adja meg adatait](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Látni fogja például

![kimeneti példa](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Mintakód
Az RRS tesztelje egy másik módja az Ügyfélkód származik. Ha a **kérelem/válasz** elemre kattint az irányítópulton, és az aljára görget, megjelenik a Python C#és az R mintakód. Megtekintheti az ERŐFORRÁSREKORDOK szintaxisát is, beleértve a kérelem URI-JÁT, fejléceit és törzsét.

Ez az útmutató bemutatja egy működő Python-példát. Módosítania kell a kísérlet **munkaterületével**, **szolgáltatásával**és **api_keyával** .

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE API KEY>"
    data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
    }
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
    headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
    body = str.encode(json.dumps(data))
    try:
        req = urllib2.Request(url, body, headers)
        response = urllib2.urlopen(req)
        result = response.read()
        print "result:" + result
            except urllib2.HTTPError, error:
        print("The request failed with status code: " + str(error.code))
        print(error.info())
        print(json.loads(error.read()))

#### <a name="test-bes-endpoint"></a>Teszt BES-végpont
Kattintson a **Batch-végrehajtás** elemre az irányítópulton, és görgessen a lap aljára. Látni fogja a mintakód C#, Python és az r segítségével A BES kéréseket olyan feladatot küld el, indítsa el a feladatot, és az egyes feladatok eredményeinek lekérése és egy feladat törlése szintaxisát is megjelenik.

Ez az útmutató bemutatja egy működő Python-példát. A kísérlet **munkaterületével**, **szolgáltatásával**és **api_keyával** kell módosítania. Emellett módosítania kell a Storage- **fiók nevét**, a **Storage-fiók kulcsát**és a **Storage-tároló nevét**. Végül a **bemeneti fájl** helyét és a **kimeneti fájl**helyét is módosítania kell.

    import urllib2
    import json
    import time
    from azure.storage import *
    workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
    service = "<REPLACE WITH YOUR SERVICE ID>"
    api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
    storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
    storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
    storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
    input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
    output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
    input_blob_name = "mydatablob.csv"
    output_blob_name = "myresultsblob.csv"
    def printHttpError(httpError):
    print("The request failed with status code: " + str(httpError.code))
    print(httpError.info())
    print(json.loads(httpError.read()))
    return
    def saveBlobToFile(blobUrl, resultsLabel):
    print("Reading the result from " + blobUrl)
    try:
        response = urllib2.urlopen(blobUrl)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    with open(output_file, "wb+") as f:
        f.write(response.read())
    print(resultsLabel + " have been written to the file " + output_file)
    return
    def processResults(result):
    first = True
    results = result["Results"]
    for outputName in results:
        result_blob_location = results[outputName]
        sas_token = result_blob_location["SasBlobToken"]
        base_url = result_blob_location["BaseLocation"]
        relative_url = result_blob_location["RelativeLocation"]
        print("The results for " + outputName + " are available at the following Azure Storage location:")
        print("BaseLocation: " + base_url)
        print("RelativeLocation: " + relative_url)
        print("SasBlobToken: " + sas_token)
        if (first):
            first = False
            url3 = base_url + relative_url + sas_token
            saveBlobToFile(url3, "The results for " + outputName)
    return

    def invokeBatchExecutionService():
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
    blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
    print("Uploading the input to blob storage...")
    data_to_upload = open(input_file, "r").read()
    blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
    print "Uploaded the input to blob storage"
    input_blob_path = "/" + storage_container_name + "/" + input_blob_name
    connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
    payload =  {
        "Input": {
            "ConnectionString": connection_string,
            "RelativeLocation": input_blob_path
        },
        "Outputs": {
            "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
        },
        "GlobalParameters": {
        }
    }
        body = str.encode(json.dumps(payload))
    headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
    print("Submitting the job...")
    # submit the job
    req = urllib2.Request(url + "?api-version=2.0", body, headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = response.read()
    job_id = result[1:-1] # remove the enclosing double-quotes
    print("Job ID: " + job_id)
    # start the job
    print("Starting the job...")
    req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    url2 = url + "/" + job_id + "?api-version=2.0"

    while True:
        print("Checking the job status...")
        # If you are using Python 3+, replace urllib2 with urllib.request in the following code
        req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
        try:
            response = urllib2.urlopen(req)
        except urllib2.HTTPError, error:
            printHttpError(error)
            return
        result = json.loads(response.read())
        status = result["StatusCode"]
        print "status:" + status
        if (status == 0 or status == "NotStarted"):
            print("Job " + job_id + " not yet started...")
        elif (status == 1 or status == "Running"):
            print("Job " + job_id + " running...")
        elif (status == 2 or status == "Failed"):
            print("Job " + job_id + " failed!")
            print("Error details: " + result["Details"])
            break
        elif (status == 3 or status == "Cancelled"):
            print("Job " + job_id + " cancelled!")
            break
        elif (status == 4 or status == "Finished"):
            print("Job " + job_id + " finished!")
            processResults(result)
            break
        time.sleep(1) # wait one second
    return
    invokeBatchExecutionService()
