---
title: Az API Management használatával AzureML webszolgáltatások kezelése |} A Microsoft Docs
description: Az API Management használatával AzureML webszolgáltatások kezelése bemutató útmutató.
keywords: a Machine learning, az api management
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 05150ae1-5b6a-4d25-ac67-fb2f24a68e8d
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 9543b1f3b10c246dadaf21b6960c7b6785fb66f4
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566380"
---
# <a name="learn-how-to-manage-azureml-web-services-using-api-management"></a>AzureML webszolgáltatások kezelése az API Management használatával
## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan gyorsan használatba az API Management az AzureML webszolgáltatások kezelése.

## <a name="what-is-azure-api-management"></a>Mi az Azure API Management?
Az Azure API Management egy Azure-szolgáltatás, amellyel a REST API-végpontokat kezelheti a felhasználói hozzáférés-szabályozás és figyelési irányítópult definiálásával. Kattintson a [Itt](https://azure.microsoft.com/services/api-management/) Azure API Management részleteiért. Kattintson a [Itt](../../api-management/api-management-get-started.md) útmutató az első lépések az Azure API Management szolgáltatással. A többi útmutató, amely a jelen útmutató alapján, termékek, a fejlesztői előfizetésekhez és a használati dashboarding további témaköreit, beleértve az értesítési konfiguráció, szint díjszabása, válasz kezelését, felhasználói hitelesítés, ismerteti.

## <a name="what-is-azureml"></a>Mi az AzureML?
Az AzureML egy Azure-szolgáltatás a machine learning, amely lehetővé teszi, hogy egyszerűen elkészítheti, telepítheti, és megoszthatja a fejlett elemzési megoldásokat. Kattintson a [Itt](https://azure.microsoft.com/services/machine-learning/) AzureML részleteiért.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató végrehajtásához lesz szüksége:

* Egy Azure-fiók. Ha nem rendelkezik Azure-fiókot, kattintson a [Itt](https://azure.microsoft.com/pricing/free-trial/) a megtudhatja, hogyan hozhat létre egy ingyenes próbafiókot.
* Az AzureML-fiók. Ha nem rendelkezik egy AzureML-fiókot, kattintson a [Itt](https://studio.azureml.net/) a megtudhatja, hogyan hozhat létre egy ingyenes próbafiókot.
* A munkaterület, a szolgáltatás és a egy webszolgáltatásként üzembe helyezett AzureML kísérlethez api_key. Kattintson a [Itt](create-experiment.md) AzureML a kísérlet létrehozásának részleteiért. Kattintson a [Itt](publish-a-machine-learning-web-service.md) számára való üzembe helyezése az AzureML kísérletezhet webszolgáltatásként. A függelék rendelkezik, azt is megteheti, létrehozása és tesztelése egy egyszerű AzureML kísérletet, és helyezze üzembe webszolgáltatásként, amely utasításokat.

## <a name="create-an-api-management-instance"></a>API Management-példány létrehozása

Az API Management-példány az Azure Machine Learning webszolgáltatás kezelheti.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **+ Erőforrás létrehozása** lehetőséget.
3. A Keresés mezőbe írja be az "API-kezelési", majd válassza az "API-kezelés" erőforráshoz.
4. Kattintson a **Create** (Létrehozás) gombra.
5. A **neve** értéket fogja hozzon létre egy egyedi URL-cím (ebben a példában használja "demoazureml").
6. Válassza ki a **előfizetés**, **erőforráscsoport**, és **hely** a szolgáltatáspéldány számára.
7. Adjon meg egy értéket **szervezetnevet** (ebben a példában használja "demoazureml").
8. Adja meg a **rendszergazdai e-mail** -e-mailt fog használni az API Management rendszer értesítései.
9. Kattintson a **Create** (Létrehozás) gombra.

Létrejön egy új szolgáltatáshoz akár 30 percig is eltarthat.

![szolgáltatás létrehozása](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Az API létrehozása
A szolgáltatáspéldány létrehozása után a a következő lépés, ha az API-t. Az API egy ügyfélalkalmazásokból meghívható műveletkészletből áll. Az API-műveleteket létező webszolgáltatásokhoz használják proxyként. Ez az útmutató a meglévő AzureML RRS- és BES-webszolgáltatásokat a proxyk API-kat hoz létre.

Az API létrehozásához:

1. Az Azure Portalon nyissa meg az imént létrehozott szolgáltatáspéldány.
2. A bal oldali navigációs panelen válassza ki **API-k**.

   ![api-management-menu](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Kattintson a **API hozzáadása**.
2. Adjon meg egy **webes API név** (ebben a példában használja "AzureML bemutató API").
3. A **webszolgáltatás URL-címe**, adja meg "`https://ussouthcentral.services.azureml.net`".
4. Adja meg a ** webes API URL-Címének utótagja ". Ez lesz az ügyfelek által használt, a kérések küldését a szolgáltatáspéldány (ebben a példában használja "azureml-demo") URL-cím utolsó része.
5. A **webes API URL-séma**válassza **HTTPS**.
6. A **termékek**válassza **alapszintű**.
7. Kattintson a **Save** (Mentés) gombra.


## <a name="add-the-operations"></a>A műveletek hozzáadása

Műveletek hozzáadása és API-khoz, a közzétevő portálon konfigurált. A közzétevő portál eléréséhez kattintson **Publisher Portalon** az API Management szolgáltatás az Azure Portalon, válassza ki a **API-k**, **Operations**, majd kattintson a **Hozzáadási művelet**.

![művelet hozzáadása](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

A **új művelet** ablak jelenik és a **aláírás** alapértelmezés szerint a program lapot választja.

## <a name="add-rrs-operation"></a>RRS művelet hozzáadása
Először hozzon létre egy műveletet az AzureML RRS szolgáltatás:

1. Az a **HTTP-műveletet**válassza **POST**.
2. Az a **URL-cím sablon**, írja be "`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`".
3. Adjon meg egy **megjelenítendő név** (ebben a példában "RRS hajtható végre").

   ![Adjon hozzá-rrs-művelet-aláírás](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Kattintson a **válaszok** > **hozzáadása** a bal oldalon, majd válassza a **200 OK**.
5. Kattintson a **mentése** menteni ezt a műveletet.

   ![Adjon hozzá-rrs-művelet-válasz](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>BES műveletek hozzáadása

> [!NOTE]
> Képernyőképek nem szerepelnek itt a BES műveletek azok nagyon hasonlóak az RRS művelet hozzáadásához.

### <a name="submit-but-not-start-a-batch-execution-job"></a>A kötegelt végrehajtási feladat elküldése (de nem indul el)

1. Kattintson a **hozzáadási művelet** egy BES művelet hozzáadása az API-t.
2. Az a **HTTP-műveletet**válassza **POST**.
3. Az a **URL-cím sablon**, írja be "`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`".
4. Adjon meg egy **megjelenítendő név** (ebben a példában "BES küldje el").
5. Kattintson a **válaszok** > **hozzáadása** a bal oldalon, majd válassza a **200 OK**.
6. Kattintson a **Save** (Mentés) gombra.

### <a name="start-a-batch-execution-job"></a>Kötegelt végrehajtási feladat indítása

1. Kattintson a **hozzáadási művelet** egy BES művelet hozzáadása az API-t.
2. Az a **HTTP-műveletet**válassza **POST**.
3. Az a **HTTP-műveletet**, írja be "`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`".
4. Adjon meg egy **megjelenítendő név** (ebben a példában "BES Start").
6. Kattintson a **válaszok** > **hozzáadása** a bal oldalon, majd válassza a **200 OK**.
7. Kattintson a **Save** (Mentés) gombra.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Az állapot vagy a kötegelt végrehajtási feladat eredménye

1. Kattintson a **hozzáadási művelet** egy BES művelet hozzáadása az API-t.
2. Az a **HTTP-műveletet**válassza **első**.
3. Az a **URL-cím sablon**, írja be "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Adjon meg egy **megjelenítendő név** (ebben a példában használja "BES állapot").
6. Kattintson a **válaszok** > **hozzáadása** a bal oldalon, majd válassza a **200 OK**.
7. Kattintson a **Save** (Mentés) gombra.

### <a name="delete-a-batch-execution-job"></a>Kötegelt végrehajtási feladat törlése

1. Kattintson a **hozzáadási művelet** egy BES művelet hozzáadása az API-t.
2. Az a **HTTP-műveletet**válassza **törlése**.
3. Az a **URL-cím sablon**, írja be "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Adjon meg egy **megjelenítendő név** (ebben a példában "BES törlése").
5. Kattintson a **válaszok** > **hozzáadása** a bal oldalon, majd válassza a **200 OK**.
6. Kattintson a **Save** (Mentés) gombra.

## <a name="call-an-operation-from-the-developer-portal"></a>Művelet meghívása a fejlesztői portálról

Közvetlenül a fejlesztői portál, amely megtekintésére és tesztelésére egy API műveleteit kényelmes módot biztosít a lehet hívni. Ebben a lépésben fogja meghívni az **RRS végrehajtása** metódushoz, amely hozzá lett adva a **AzureML bemutató API**. 

1. Kattintson a **fejlesztői portál**.

   ![fejlesztői – portál](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Kattintson a **API-k** a felső menüben, és kattintson a **AzureML bemutató API** a rendelkezésre álló műveletek megjelenítéséhez.

   ![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Válassza ki **RRS végrehajtása** a művelethez. Kattintson a **kipróbálás**.

   ![try-it](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. A **kérés paraméterei**, írja be a **munkaterület** és **szolgáltatás**, írja be "2.0 a **API-verzió**, és a"true"az a **részletek**. Annak a **munkaterület** és **szolgáltatás** az AzureML webszolgáltatás irányítópultján (lásd: **a webszolgáltatás teszteléséhez** függelék).

   A **Kérésfejlécek**, kattintson a **fejléc hozzáadása** , és írja be a "Content-Type" és "application/json". Kattintson a **fejléc hozzáadása** újra és írja be az "Engedélyezés" és "tulajdonosi  *\<a szolgáltatás API-kulcs\>*". Az API-KULCSÁT az AzureML webszolgáltatási irányítópultján található (lásd: **a webszolgáltatás teszteléséhez** függelék).

   A **kérelem törzse**, típus `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`.

   ![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Kattintson a **küldése**.

   ![Küldés](./media/manage-web-service-endpoints-using-api-management/send.png)

Művelet meghívása után a fejlesztői portál megjeleníti a **kért URL-cím** a háttér-szolgáltatás a **válasz állapota**, a **válaszfejlécek**, és bármely  **Válasz tartalma**.

![Válasz-állapota](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>A függelék – létrehozása és tesztelése egy egyszerű AzureML webszolgáltatás
### <a name="creating-the-experiment"></a>A kísérlet létrehozása
Az alábbiakban az AzureML egyszerű kísérlet létrehozására és központi telepítésére, webszolgáltatásként lépéseket. A web service veszi adjon meg egy tetszőleges szöveges oszlop és funkciókat egészként képviselt készletét adja vissza. Példa:

| Szöveg | Kivonatolt szöveg |
| --- | --- |
| Ez a jó naponta |1 1 2 2 0 2 0 1 |

Először a kiválasztott, egy böngészőben nyissa meg azt: [ https://studio.azureml.net/ ](https://studio.azureml.net/) , és adja meg a hitelesítő adataival, és jelentkezzen be. Ezután hozzon létre egy új üres kísérlet.

![a sablonok kísérlet keresése](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Nevezze át, hogy **SimpleFeatureHashingExperiment**. Bontsa ki a **mentett adatkészletek** , és húzza **könyv ismertetők az Amazon** alakzatot a kísérlet során.

![egyszerű – a szolgáltatás-kivonatolás – kísérlet](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Bontsa ki a **adatátalakítás** és **adatkezelési** , és húzza **Select Columns in Dataset** alakzatot a kísérlet során. Csatlakozás **felülvizsgálatok lefoglalja az Amazon** való **oszlopok kiválasztása az adathalmaz**.

![select-columns](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Kattintson a **Select Columns in Dataset** majd **Oszlopválasztás indítása** válassza **Col2**. Kattintson a pipa jelre a módosítások alkalmazásához.

![select-columns](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Bontsa ki a **Szövegelemzés** , és húzza **Szolgáltatáskivonatolás** alakzatot a kísérletet. Csatlakozás **oszlopok kiválasztása az adathalmaz** való **Szolgáltatáskivonatolás**.

![Connect-projekt-oszlopok](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Típus **3** számára a **bitsize kivonatoláshoz**. Ekkor létrejön a 8 (23) oszlopot.

![kivonatoló bitsize](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

Ezen a ponton érdemes kattintson **futtatása** , tesztelje a kísérletét.

![Futtatás](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Webszolgáltatás létrehozása
Most hozzon létre egy webes szolgáltatás. Bontsa ki a **webszolgáltatás** , és húzza **bemeneti** alakzatot a kísérlet során. Csatlakozás **bemeneti** való **Szolgáltatáskivonatolás**. Húzással is **kimeneti** alakzatot a kísérlet során. Csatlakozás **kimeneti** való **Szolgáltatáskivonatolás**.

![kimeneti-az--szolgáltatáskivonatolás](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Kattintson a **webszolgáltatás közzététele**.

![publish-web-service](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Kattintson a **Igen** közzététele a kísérletet.

![Igen – közzététel](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>A webszolgáltatás teszteléséhez
Az AzureML webszolgáltatás (kérelem/válasz szolgáltatás) az RSS- és BES (kötegelt végrehajtási szolgáltatás) végpontok áll. Az RSS szinkron végrehajtásra van. BES az aszinkron feladatok végrehajtásra. Az alábbi minta Python-forrás a webes szolgáltatás tesztelése, szükség lehet töltse le és telepítse az Azure SDK a Pythonhoz készült (lásd: [Python telepítése](../../python-how-to-install.md)).

Is szüksége lesz a **munkaterület**, **szolgáltatás**, és **api_key** a kísérlet a minta az alábbi forrás. Annak a munkaterület és a szolgáltatás lehet **kérés/válasz** vagy **kötegelt végrehajtási** a webszolgáltatás irányítópultján a kísérlethez.

![find-workspace-and-service](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Annak a **api_key** kattintva is futtathatja a kísérletet a webszolgáltatás irányítópultján.

![Keresés – api-kulcs](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Teszt RRS-végpont
##### <a name="test-button"></a>Teszt gomb
Egy egyszerű módja annak, hogy az RRS-végpont tesztelése, hogy **tesztelése** webszolgáltatási irányítópultján található.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

Típus **Ez a jó naponta** a **col2**. Kattintson a pipa jelre.

![Adja meg adatait](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Látni fogja például

![kimeneti példa](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Mintakód
Az RRS tesztelje egy másik módja az Ügyfélkód származik. Ha rákattint **kérés/válasz** meg az irányítópultot, és görgessen le, látni fogja a mintakód C#, Python és az r segítségével Az RRS-kérelem, a kérelem URI-t, beleértve a szintaxist is megjelennek a fejlécek és törzs.

Ez az útmutató bemutatja egy működő Python-példát. Módosítani kell a **munkaterület**, **szolgáltatás**, és **api_key** a kísérlet.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
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
Kattintson a **kötegelt végrehajtás** az irányítópult-és alján görgessen. Látni fogja a mintakód C#, Python és az r segítségével A BES kéréseket olyan feladatot küld el, indítsa el a feladatot, és az egyes feladatok eredményeinek lekérése és egy feladat törlése szintaxisát is megjelenik.

Ez az útmutató bemutatja egy működő Python-példát. Módosítani kell a **munkaterület**, **szolgáltatás**, és **api_key** a kísérlet. Ezenkívül módosítani kell a **tárfióknevet**, **tárfiókkulcs**, és **tároló neve**. Végül kell módosítani a helyét a **bemeneti fájl** és helye a **kimeneti fájl**.

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
    with open(output_file, "w+") as f:
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
