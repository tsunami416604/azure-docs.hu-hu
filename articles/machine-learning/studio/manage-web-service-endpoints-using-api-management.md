---
title: Webszolgáltatások kezelése az API Management használatával
titleSuffix: ML Studio (classic) - Azure
description: Útmutató az AzureML-webszolgáltatások API Management használatával történő kezeléséhez. A REST API-végpontok kezelése a felhasználói hozzáférés, a használati szabályozás és az irányítópult figyelésének meghatározásával.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: cbe01ee9b8edeab349db484cea6c25dca32bf213
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218028"
---
# <a name="manage-azure-machine-learning-studio-classic-web-services-using-api-management"></a>Az Azure Machine Learning Studio (klasszikus) webes szolgáltatásainak kezelése az API Management használatával

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan kezdheti el gyorsan az API Management használatával az Azure Machine Learning Studio (klasszikus) webszolgáltatások kezelését.

## <a name="what-is-azure-api-management"></a>Mi az Azure API Management?
Az Azure API Management egy Azure-szolgáltatás, amely lehetővé teszi a REST API-végpontok kezelését a felhasználói hozzáférés, a használati szabályozás és az irányítópult figyelésének meghatározásával. További részletekért tekintse meg az [Azure API-felügyeleti webhelyet.](https://azure.microsoft.com/services/api-management/) Az Azure API Management első lépéseiaz [importálási és közzétételi útmutatóban.](/azure/api-management/import-and-publish) Ez az útmutató, amelyen ez az útmutató alapul, további témaköröket tartalmaz, beleértve az értesítési konfigurációkat, a rétegszerinti díjszabást, a válaszkezelést, a felhasználói hitelesítést, a termékek létrehozását, a fejlesztői előfizetéseket és a használati irányítópultokat.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató befejezéséhez a következőkre van szükség:

* Egy Azure-fiók.
* Egy AzureML-fiók.
* A munkaterület, a szolgáltatás és a api_key egy webszolgáltatásként üzembe helyezett AzureML-kísérlethez. Az AzureML-kísérletek létrehozásáról a Studio rövid útmutatójában talál [részleteket.](create-experiment.md) A Studio (klasszikus) kísérlet webszolgáltatásként való üzembe helyezéséről a [Studio üzembe helyezési útmutatójában](deploy-a-machine-learning-web-service.md) talál további információt az AzureML-kísérletek webszolgáltatásként való üzembe helyezéséről. Az A függelék utasításokat is tartalmaz egy egyszerű AzureML-kísérlet létrehozásához és teszteléséhez, és webszolgáltatásként való üzembe helyezéséhez.

## <a name="create-an-api-management-instance"></a>API Management-példány létrehozása

Az Azure Machine Learning webszolgáltatás egy API Management-példány segítségével kezelhető.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza a **+ Erőforrás létrehozása** lehetőséget.
3. A keresőmezőbe írja be az "API-kezelés" kifejezést, majd válassza ki az "API-kezelés" erőforrást.
4. Kattintson **a Létrehozás gombra.**
5. A **Név** érték egy egyedi URL létrehozásához lesz használva (ebben a példában a "demoazureml").
6. Válasszon **egy előfizetést**, **erőforráscsoportot**és **helyet** a szolgáltatáspéldányhoz.
7. Adjon meg egy értéket a **szervezet nevéhez** (ez a példa a "demoazureml").
8. Adja meg **a rendszergazdai e-mail -** ezt az e-mailt fogja használni az API Management rendszer értesítéseihez.
9. Kattintson **a Létrehozás gombra.**

Egy új szolgáltatás létrehozása akár 30 percet is igénybe vehet.

![létrehozás-szolgáltatás](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Az API létrehozása
A szolgáltatáspéldány létrehozása után a következő lépés az API létrehozása. Az API egy ügyfélalkalmazásokból meghívható műveletkészletből áll. Az API-műveleteket létező webszolgáltatásokhoz használják proxyként. Ez az útmutató olyan API-kat hoz létre, amelyek proxyt hoznak létre a meglévő AzureML RRS- és BES-webszolgáltatásokhoz.

Az API létrehozása:

1. Az Azure Portalon nyissa meg a létrehozott szolgáltatáspéldányt.
2. A bal oldali navigációs ablakban válassza az **API-k**lehetőséget.

   ![api-management-menü](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Kattintson **az API hozzáadása**gombra.
2. Adjon meg egy **webes API-nevet** (ez a példa az "AzureML Demo API"-t használja).
3. A **webszolgáltatás URL-címéhez**írja be a "`https://ussouthcentral.services.azureml.net`" (" (" értéket.
4. Adjon meg egy **Web API URL-utótagot". Ez lesz az URL-cím utolsó része, amelyet az ügyfelek a szolgáltatáspéldánynak küldött kérelmek küldéséhez használnak (ebben a példában az "azureml-demo").
5. A **webes API URL-séma**esetén válassza a **HTTPS**lehetőséget.
6. **Termékek esetén**válassza az **Indító**lehetőséget.
7. Kattintson a **Mentés** gombra.


## <a name="add-the-operations"></a>A műveletek hozzáadása

A műveletek hozzáadódnak és konfigurálva vannak egy API-hoz a közzétevői portálon. A közzétevői portál eléréséhez kattintson a **Publisher portál** az Api Management szolgáltatás Hoz az Azure Portalon, válassza **az API-k**, **Műveletek**, majd kattintson a Művelet **hozzáadása gombra.**

![kiegészítő művelet](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

Megjelenik **az Új művelet** ablak, és alapértelmezés szerint az **Aláírás** lap lesz kiválasztva.

## <a name="add-rrs-operation"></a>RRS-művelet hozzáadása
Először hozzon létre egy műveletet az AzureML RRS szolgáltatáshoz:

1. A **HTTP-művelethez**válassza a **POST**lehetőséget.
2. Az **URL-sablonhoz**`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`írja be a következőt: ".
3. Adjon meg egy **megjelenítendő nevet** (ez a példa az "RRS Execute"-t használja).

   ![add-rrs-operation-signature](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Kattintson a bal oldalon a **Válaszok** > **hozzáadása** gombra, és válassza a **200 OK gombot.**
5. A művelet mentéséhez kattintson a **Mentés** gombra.

   ![add-rrs-operation-válasz](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>BES-műveletek hozzáadása

> [!NOTE]
> A KÉPERNYŐképek itt nem szerepelnek a BES-műveletekhez, mivel nagyon hasonlítanak az RRS-művelet hozzáadásához.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Kötegelt végrehajtási feladat küldése (de nem indítása)

1. Kattintson **a Művelet hozzáadása gombra,** ha BES-műveletet szeretne hozzáadni az API-hoz.
2. A **HTTP-művelethez**válassza a **POST**lehetőséget.
3. Az **URL-sablonhoz**`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`írja be a következőt: ".
4. Adjon meg **egy megjelenítendő nevet** (ez a példa a "BES Submit" értéket használja).
5. Kattintson a bal oldalon a **Válaszok** > **hozzáadása** gombra, és válassza a **200 OK gombot.**
6. Kattintson a **Mentés** gombra.

### <a name="start-a-batch-execution-job"></a>Kötegelt végrehajtási feladat indítása

1. Kattintson **a Művelet hozzáadása gombra,** ha BES-műveletet szeretne hozzáadni az API-hoz.
2. A **HTTP-művelethez**válassza a **POST**lehetőséget.
3. A **HTTP-művelethez**`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`írja be a " ".
4. Adjon meg **egy megjelenítendő nevet** (ez a példa a "BES Start" nevet használja).
6. Kattintson a bal oldalon a **Válaszok** > **hozzáadása** gombra, és válassza a **200 OK gombot.**
7. Kattintson a **Mentés** gombra.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Kötegelt végrehajtási feladat állapotának vagy eredményének beszereznie

1. Kattintson **a Művelet hozzáadása gombra,** ha BES-műveletet szeretne hozzáadni az API-hoz.
2. A **HTTP-műveletnél**válassza a **GET**lehetőséget.
3. Az **URL-sablonhoz**`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`írja be a következőt: ".
4. Adjon meg **egy megjelenítendő nevet** (ez a példa a "BES-állapot" nevet használja).
6. Kattintson a bal oldalon a **Válaszok** > **hozzáadása** gombra, és válassza a **200 OK gombot.**
7. Kattintson a **Mentés** gombra.

### <a name="delete-a-batch-execution-job"></a>Kötegelt végrehajtási feladat törlése

1. Kattintson **a Művelet hozzáadása gombra,** ha BES-műveletet szeretne hozzáadni az API-hoz.
2. A **HTTP-művelethez**válassza a **DELETE**lehetőséget.
3. Az **URL-sablonhoz**`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`írja be a következőt: ".
4. Adjon meg **egy megjelenítendő nevet** (ez a példa a "BES Delete" értéket használja).
5. Kattintson a bal oldalon a **Válaszok** > **hozzáadása** gombra, és válassza a **200 OK gombot.**
6. Kattintson a **Mentés** gombra.

## <a name="call-an-operation-from-the-developer-portal"></a>Művelet felhívása a fejlesztői portálról

Műveletek hívható közvetlenül a fejlesztői portálon, amely kényelmes módja az API-műveletek megtekintéséhez és teszteléséhez. Ebben a lépésben meg hívja az **AzureML Demó API-hoz**hozzáadott **RRS-végrehajtási** metódust. 

1. Kattintson **a Fejlesztői portál elemre.**

   ![fejlesztő-portál](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Kattintson az **API-k** a felső menüben, majd kattintson az **AzureML Demo API** az elérhető műveletek megtekintéséhez.

   ![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Válassza az **RRS Végrehajtás lehetőséget** a művelethez. Kattintson **a Próbálja ki gombra.**

   ![kipróbál- ez](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. A **Kérelem paraméterek**mezőbe írja be a **munkaterületet** és a **szolgáltatást,** írja be a "2.0 az **apiversion**, és az "igaz" **értéket.** A **munkaterületet** és a **szolgáltatást** az AzureML webszolgáltatás irányítópultján találja (lásd: **A webszolgáltatás tesztelése az** A függelékben).

   A **Kérelemfejlécek**lapon kattintson **a Fejléc hozzáadása** gombra, és írja be a "Content-Type" és a "application/json" kifejezést. Kattintson ismét **a Fejléc hozzáadása** gombra, és írja be * \<az\>*"Engedélyezés" és a "A szolgáltatás API-KEY" szövegét. Az API-KEY az AzureML webszolgáltatás irányítópultján található **(lásd: A webszolgáltatás tesztelése az** A függelékben).

   A **kérelem törzséhez**írja be a típust. `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`

   ![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Kattintson a **Küldés** gombra.

   ![Küldj](./media/manage-web-service-endpoints-using-api-management/send.png)

Egy művelet meghívása után a fejlesztői portál megjeleníti a **kért URL-címet** a háttérszolgáltatástól, a **Válasz állapotát**, a **Válasz fejléceket**és a válasz **tartalmat.**

![válasz-állapot](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>A függelék - Egyszerű AzureML-webszolgáltatás létrehozása és tesztelése
### <a name="creating-the-experiment"></a>A kísérlet létrehozása
Az alábbiakban egy egyszerű AzureML-kísérlet létrehozásának és webszolgáltatásként való üzembe helyezésének lépéseit olvashatja. A webszolgáltatás tetszőleges szövegoszlopként egy tetszőleges szövegoszlopot vesz fel, és egész számként ábrázolt funkciókat ad vissza. Példa:

| Szöveg | Kivonatolt szöveg |
| --- | --- |
| Ez egy jó nap. |1 1 2 2 0 2 0 1 |

Először egy ön által választott böngészővel keresse meg a következőket, [https://studio.azureml.net/](https://studio.azureml.net/) és adja meg a hitelesítő adatait a bejelentkezéshez. Ezután hozzon létre egy új üres kísérletet.

![keresés-kísérlet-sablonok](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Nevezze át **SimpleFeatureHashingExperiment .** Bontsa ki **a mentett adatkészleteket,** és húzza a **Könyvértékeléseket** az Amazonról a kísérletre.

![egyszerű-funkció-hash-kísérlet](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Bontsa ki **az Adatátalakítás** és **-kezelés csomópontot,** és húzza **az Oszlopok kijelölése az adatkészletben** a kísérletbe. Csatlakoztassa **a könyvértékeléseket az Amazon-tól** az **oszlopok kijelöléséhez az adatkészletben.**

![A könyvvéleményezési adatkészletmodul csatlakoztatása projektoszlopok modulhoz](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Kattintson **az Oszlopok kijelölése az adatkészletben,** majd az **Indítás oszlopkijelölő** gombra, és válassza a **Col2**lehetőséget. A módosítások alkalmazásához kattintson a pipára.

![Oszlopok kijelölése oszlopnevekkel](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Bontsa ki **a Szövegelemzés** csomópontot, és húzza a **szolgáltatáskivonatolást** a kísérletre. Az **adatkészlet Ben select columns (Oszlopok kijelölése)** összekapcsolása a **szolgáltatáskivonatoláshoz.**

![csatlakozás-projektoszlopok](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Írja be a **3-as** típust a **kivonatoló bitmérethez.** Ez 8 (23) oszlopot hoz létre.

![kivonatolás-bitméret](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

Ezen a ponton érdemes lehet a **Futtatás** gombra kattintani a kísérlet teszteléséhez.

![Fuss](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Webszolgáltatás létrehozása
Most hozzon létre egy webszolgáltatást. Bontsa ki **a Webszolgáltatás csomópontját,** és húzza a **bevitelt** a kísérletre. **A bemeneti bevitel** csatlakoztatása a **szolgáltatáskivonatozáshoz.** Húzza a **kimenetet** a kísérletre is. **A kimenet** csatlakoztatása **a szolgáltatáskivonatozáshoz.**

![kimenet-szolgáltatás-kivonatolás](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Kattintson **a Webszolgáltatás közzététele gombra.**

![közzététel-web-szolgáltatás](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

A kísérlet közzétételéhez kattintson az **Igen** gombra.

![Igen-to-közzé](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>A webszolgáltatás tesztelése
Az AzureML-webszolgáltatás RSS (request/response service) és BES (batch execution service) végpontokból áll. Az RSS szinkron végrehajtásra alkalmas. A BES az aszinkron feladatvégrehajtáshoz használható. Ha tesztelni szeretné a webszolgáltatást az alábbi minta Python-forrással, előfordulhat, hogy le kell töltenie és telepítenie kell az Azure SDK python-t (lásd: [Python telepítése).](/azure/python/python-sdk-azure-install)

Szüksége lesz a **munkaterületre,** a **szolgáltatásra**és **a kísérlet api_key** az alábbi mintaforráshoz. A munkaterületet és a szolgáltatást a webszolgáltatás irányítópultján a **Kérés/válasz** vagy a **Kötegelt végrehajtás** elemre kattintva találhatja meg.

![find-workspace-and-service](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

A **api_key** a kísérletre kattintva, a webszolgáltatás irányítópultján találhatja meg.

![find-api-kulcs](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>RrS-végpont tesztelése
##### <a name="test-button"></a>Teszt gomb
Az RRS-végpont tesztelésének egyszerű módja, ha a **webszolgáltatás** irányítópultján a Tesztelés gombra kattint.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

Típus **Ez egy jó nap** **col2**. Kattintson a pipa.

![adatok bevitele](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Látni fogja, valami ilyesmi

![mintakimenet](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Példakód
Egy másik módja annak, hogy tesztelje az RRS az ügyfél kódot. Ha az irányítópulton a **Kérés/válasz** elemre kattint, és az alsó részre görget, megjelenik a C#, python és R mintakódja. Az RRS-kérelem szintaxisa is megjelenik, beleértve a kérelem URI-ját, a fejléceket és a törzset.

Ez az útmutató egy működő Python-példát mutat be. Módosítania kell azt a **munkaterülettel,** a **szolgáltatással**és **a kísérlet api_key.**

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

#### <a name="test-bes-endpoint"></a>BES-végpont tesztelése
Kattintson a **Kötegelt végrehajtás** elemre az irányítópulton, és görgessen az aljára. Látni fogja a C#, Python és R mintakódját. Látni fogja a BES-kérelmek szintaxisát is, hogy küldjenek el egy feladatot, indítsanak el egy feladatot, megkapják egy feladat állapotát vagy eredményeit, és töröljenek egy feladatot.

Ez az útmutató egy működő Python-példát mutat be. Módosítania kell azt a **munkaterülettel,** **a szolgáltatással**és **a kísérlet api_key.** Ezenkívül módosítania kell a **tárfiók nevét**, **a tárfiók kulcsát**és a **tárolótároló nevét.** Végül módosítania kell a **bemeneti fájl** helyét és a **kimeneti fájl**helyét.

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
