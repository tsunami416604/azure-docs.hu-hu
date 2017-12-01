---
title: "Az API Management használata AzureML webszolgáltatások kezelése |} Microsoft Docs"
description: "Egy útmutató bemutatja, hogyan kezelheti az AzureML-webszolgáltatások API Management használata."
keywords: "gépi tanulási, az api management"
services: machine-learning
documentationcenter: 
author: roalexan
manager: jhubbard
editor: 
ms.assetid: 05150ae1-5b6a-4d25-ac67-fb2f24a68e8d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: roalexan
ms.openlocfilehash: b2c9f53de1abd2aea5fabbefecc5bbb144148a7b
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="learn-how-to-manage-azureml-web-services-using-api-management"></a>AzureML webszolgáltatások kezelése az API Management használatával
## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan gyorsan megkezdheti az API Management az AzureML webszolgáltatások kezeli.

## <a name="what-is-azure-api-management"></a>Mi az Azure API Management?
Az Azure API-kezelés nem az Azure-szolgáltatások, amely lehetővé teszi a REST API-végpontokat kezelheti a felhasználói hozzáférés-szabályozás és figyelési irányítópult meghatározásával. Kattintson a [Itt](https://azure.microsoft.com/services/api-management/) Azure API Management leírását. Kattintson a [Itt](../../api-management/api-management-get-started.md) az Azure API Management az első lépések útmutató. Ez más útmutató, ez az útmutató alapján, minden olyan további témaköreit, beleértve az értesítési konfigurációk, a árképzési szint, a válasz kezelése, a felhasználói hitelesítést, termékek, a fejlesztői előfizetésekhez és a használati dashboarding létrehozása.

## <a name="what-is-azureml"></a>Mi az az AzureML?
Az AzureML az Azure-szolgáltatások a gépi tanulási, amelynek segítségével könnyedén létre, telepíthetnek és speciális elemzési megoldások megosztása. Kattintson a [Itt](https://azure.microsoft.com/services/machine-learning/) AzureML leírását.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató, szüksége:

* Egy Azure-fiók. Ha nem rendelkezik Azure-fiókot, kattintson a [Itt](https://azure.microsoft.com/pricing/free-trial/) talál részletes egy ingyenes próbafiók létrehozásához.
* Az AzureML-fiók. Ha nincs AzureML fiókja, kattintson a [Itt](https://studio.azureml.net/) talál részletes egy ingyenes próbafiók létrehozásához.
* A munkaterületen, a szolgáltatás és a api_key egy webes szolgáltatásként telepített AzureML kísérleti fázisú funkciókat. Kattintson a [Itt](create-experiment.md) talál részletes AzureML kísérlet létrehozásához. Kattintson a [Itt](publish-a-machine-learning-web-service.md) vonatkozó részletes információt az AzureML telepítendő kísérletezhet webszolgáltatásként. Alternatív megoldásként a melléklet rendelkezik létrehozása és tesztelése egy egyszerű AzureML kísérletet, és egy webszolgáltatás üzembe vonatkozó utasításokat.

## <a name="create-an-api-management-instance"></a>API Management-példány létrehozása

Kezelheti az Azure Machine Learning webszolgáltatásba az API Management-példánnyal.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **+ hozzon létre egy erőforrást**.
3. A keresési mezőbe írja be a "API-felügyelet", majd válassza ki a "API-kezelés" erőforrást.
4. Kattintson a **Create** (Létrehozás) gombra.
5. A **neve** érték használandó hozzon létre egy egyedi URL-címet (a példában az "demoazureml").
6. Válassza ki a **előfizetés**, **erőforráscsoport**, és **hely** a szolgáltatáspéldány.
7. Adjon meg egy értéket **szervezetnév** (a példában az "demoazureml").
8. Adja meg a **rendszergazdai e-mail** -értesítést kapnak az API Management rendszer az e-mailt fog történni.
9. Kattintson a **Create** (Létrehozás) gombra.

A létrehozandó új szolgáltatáshoz akár 30 percet is igénybe vehet.

![szolgáltatás létrehozása](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Az API-t létrehozni
A szolgáltatáspéldány létrehozása után a következő lépésre az API-t létrehozni. Az API egy ügyfélalkalmazásokból meghívható műveletkészletből áll. Az API-műveleteket létező webszolgáltatásokhoz használják proxyként. Ez az útmutató a meglévő AzureML RR-EKET és BES webszolgáltatásokhoz proxybeállítások API-kat hoz létre.

Az API létrehozása:

1. Nyissa meg az imént létrehozott szolgáltatáspéldány az Azure-portálon.
2. A bal oldali navigációs ablakból válassza **API-k**.

   ![API-felügyeleti-menü](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Kattintson a **API hozzáadása**.
2. Adjon meg egy **webes API-név** (a példában az "AzureML bemutató API").
3. A **webszolgáltatás URL-címe**, adja meg "`https://ussouthcentral.services.azureml.net`".
4. Adjon meg egy ** webes API URL-címe utótag ". Ez az URL-címet használó ügyfelek kérelmek küldéséhez a szolgáltatáspéldány (a példában az "azureml-bemutató") utolsó része lesz.
5. A **webes API URL-séma**, jelölje be **HTTPS**.
6. A **termékek**, jelölje be **alapszintű**.
7. Kattintson a **Save** (Mentés) gombra.


## <a name="add-the-operations"></a>A műveletek hozzáadása

Műveletek felvétele, illetve egy API-t, a közzétevő portálon konfigurálva. A közzétevő portál eléréséhez kattintson **Publisher portal** az API Management szolgáltatás az Azure portálon, válassza ki **API-k**, **műveletek**, kattintson a **Hozzáadási művelet**.

![Adja hozzá művelet](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

A **új művelet** ablak megjelenik, és a **aláírás** alapértelmezett kiválasztása lapon.

## <a name="add-rrs-operation"></a>RR-EKET művelet hozzáadása
Először létre kell hoznia egy műveletet az AzureML RR-EKET szolgáltatáshoz:

1. Az a **HTTP-műveletet**, jelölje be **POST**.
2. Az a **URL-cím sablon**, típusa "`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`".
3. Adjon meg egy **megjelenített név** (a példában az "RR-EKET hajtható végre").

   ![Adja hozzá-rrs-művelet-aláírása](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Kattintson a **válaszok** > **hozzáadása** a bal oldalon válassza ki **200 OK**.
5. Kattintson a **mentése** menti ezt a műveletet.

   ![Adja hozzá-rrs-művelet-válasz](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Adja hozzá a BES műveletek

> [!NOTE]
> Képernyőfelvételek nem ide tartoznak a BES műveletek szerint nagyon hasonló a hozzáadása a RR-EKET műveletet.

### <a name="submit-but-not-start-a-batch-execution-job"></a>A kötegelt végrehajtási feladatok elküldése (de indul el)

1. Kattintson a **hozzáadási művelet** a BES művelet hozzáadása az API-t.
2. Az a **HTTP-műveletet**, jelölje be **POST**.
3. Az a **URL-cím sablon**, típusa "`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`".
4. Adjon meg egy **megjelenített név** (a példában az "BES küldje el").
5. Kattintson a **válaszok** > **hozzáadása** a bal oldalon válassza ki **200 OK**.
6. Kattintson a **Save** (Mentés) gombra.

### <a name="start-a-batch-execution-job"></a>Kötegelt végrehajtási feladat indítása

1. Kattintson a **hozzáadási művelet** a BES művelet hozzáadása az API-t.
2. Az a **HTTP-műveletet**, jelölje be **POST**.
3. Az a **HTTP-műveletet**, típusa "`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`".
4. Adjon meg egy **megjelenített név** (a példában az "BES Start").
6. Kattintson a **válaszok** > **hozzáadása** a bal oldalon válassza ki **200 OK**.
7. Kattintson a **Save** (Mentés) gombra.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Az állapot vagy a kötegelt végrehajtási feladat eredménye

1. Kattintson a **hozzáadási művelet** a BES művelet hozzáadása az API-t.
2. Az a **HTTP-műveletet**, jelölje be **beolvasása**.
3. Az a **URL-cím sablon**, típusa "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Adjon meg egy **megjelenített név** (a példában az "BES állapota").
6. Kattintson a **válaszok** > **hozzáadása** a bal oldalon válassza ki **200 OK**.
7. Kattintson a **Save** (Mentés) gombra.

### <a name="delete-a-batch-execution-job"></a>A kötegelt végrehajtási feladatok törlése

1. Kattintson a **hozzáadási művelet** a BES művelet hozzáadása az API-t.
2. Az a **HTTP-műveletet**, jelölje be **törlése**.
3. Az a **URL-cím sablon**, típusa "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Adjon meg egy **megjelenített név** (a példában az "BES Delete").
5. Kattintson a **válaszok** > **hozzáadása** a bal oldalon válassza ki **200 OK**.
6. Kattintson a **Save** (Mentés) gombra.

## <a name="call-an-operation-from-the-developer-portal"></a>Egy művelet hívása a Developer portálról

Műveletek hívható közvetlenül a megtekintése, és az API-k működésének teszteléséhez kényelmes megoldást kínál Developer portálon. Ebben a lépésben fogja hívni a **RRS hajtható végre** módszerrel, amely hozzá lett adva a **AzureML bemutató API**. 

1. Kattintson a **fejlesztői portálján**.

   ![fejlesztői-portál](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Kattintson a **API-k** a felső menüben, majd kattintson a **AzureML bemutató API** a rendelkezésre álló műveletek megjelenítéséhez.

   ![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Válassza ki **RRS hajtható végre** a művelethez. Kattintson a **kipróbálás**.

   ![Próbálja meg-it](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. A **kérelem paraméterei**, típusa a **munkaterület** és **szolgáltatás**, típusa "2.0 a **apiversion**, és a"true", az a **részletek**. Megtalálhatja a **munkaterület** és **szolgáltatás** az AzureML webes szolgáltatás irányítópultján (lásd: **tesztelése a webszolgáltatás** függelék).

   A **kérelem fejlécei**, kattintson a **Hozzáadás fejléc** , és írja be a "Content-Type" és "application/json". Kattintson a **Hozzáadás fejléc** újra és írja be az "Engedélyezés" és "tulajdonosi  *\<a szolgáltatás API-kulcs\>*". Az API-kulcsot az AzureML webes szolgáltatás irányítópultján található (lásd: **tesztelése a webszolgáltatás** függelék).

   A **Request body**, típus `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`.

   ![az azureml-bemutató-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Kattintson a **küldése**.

   ![Küldés](./media/manage-web-service-endpoints-using-api-management/send.png)

Után egy művelet indítása, a fejlesztői portál megjeleníti-e a **a kért URL-cím** a háttér-szolgáltatás a **válaszállapot**, a **válaszfejlécek**, és az egyik **válasz tartalom**.

![válasz-állapot](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>A függelék – létrehozása és tesztelése egy egyszerű AzureML webszolgáltatás
### <a name="creating-the-experiment"></a>A kísérlet létrehozása
Az alábbiakban az AzureML egyszerű kísérlet létrehozását és telepítését webszolgáltatásként lépéseit. A webes szolgáltatás vesz igénybe, adja meg a tetszőleges szöveget, és adja vissza egész számok-ként funkciókat. Példa:

| Szöveg | Kivonatolt szöveg |
| --- | --- |
| Ez az jó naponta |1 1 2 2 0 2 0 1 |

Először egy böngészőt, nyissa meg azt: [https://studio.azureml.net/](https://studio.azureml.net/) bejelentkezési hitelesítő adataival. Ezután hozzon létre egy új üres kísérlet.

![Keresés – kísérlet-sablonok](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Nevezze át **SimpleFeatureHashingExperiment**. Bontsa ki a **mentett adatkészletek** , és húzza **könyv értékelést az Amazon** alakzatot a kísérlet során.

![egyszerű-szolgáltatás-kivonatolás-kísérlet](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Bontsa ki a **Data Transformation** és **adatkezelési** , és húzza **Select Columns in Dataset** alakzatot a kísérlet során. Csatlakozás **értékelést le az Amazon** való **oszlopok kiválasztása az adathalmaz**.

![select-columns](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Kattintson a **Select Columns in Dataset** majd **Oszlopválasztás** válassza **Col2**. Kattintson a pipa ikonra, hogy alkalmazza a módosításokat.

![select-columns](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Bontsa ki a **Szövegelemzések** , és húzza **Szolgáltatáskivonatolás** alakzatot a kísérletet. Csatlakozás **oszlopok kiválasztása az adathalmaz** való **Szolgáltatáskivonatolás**.

![Connect--projektoszlopok](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Típus **3** a a **bitsize kivonatoláshoz**. Ezzel létrehoz 8 (23) oszlopot.

![kivonatoló bitsize](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

Ezen a ponton érdemes lehet kattintson **futtatása** tesztelése a kísérletet.

![Futtatás](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Webszolgáltatás létrehozása
Most hozzon létre egy webszolgáltatás-bővítmény. Bontsa ki a **webszolgáltatás** , és húzza **bemeneti** alakzatot a kísérlet során. Csatlakozás **bemeneti** való **Szolgáltatáskivonatolás**. Húzással is **kimeneti** alakzatot a kísérlet során. Csatlakozás **kimeneti** való **Szolgáltatáskivonatolás**.

![kimeneti-az-szolgáltatás-kivonatolás](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Kattintson a **webszolgáltatás**.

![közzététel-webszolgáltatás](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Kattintson a **Igen** közzététele a kísérletet.

![Igen – közzététele](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>A webszolgáltatás tesztelése
Az AzureML webszolgáltatás RSS (kérelem/válasz szolgáltatás) és a BES (kötegelt végrehajtási szolgáltatás) végpontok áll. RSS szinkron végrehajtásra van. BES aszinkron feladat végrehajtása szolgál. A webes szolgáltatás, az alábbi minta Python-forrással rendelkező tesztelése, esetleg töltse le és telepítse az Azure SDK Python (lásd: [Python telepítése](../../python-how-to-install.md)).

Konfigurálnia kell a **munkaterület**, **szolgáltatás**, és **api_key** a kísérlet az alábbi minta adatforrásra vonatkozóan. Megtalálhatja az munkaterület és a szolgáltatás lehet **kérelem/válasz** vagy **kötegelt végrehajtási** webes szolgáltatás irányítópultján a kísérleti fázisú funkciókat.

![Keresés – munkaterület-és-szolgáltatás](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Megtalálhatja az **api_key** webes szolgáltatás irányítópultján kísérletbe kattintva.

![Keresés – api-kulcs](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Teszt RR-EKET végpont
##### <a name="test-button"></a>Teszt gomb
Az RRS végpont teszteléséhez egyszerűen: kattintson **tesztelése** a webes szolgáltatás irányítópultján.

![teszt](./media/manage-web-service-endpoints-using-api-management/test.png)

Típus **Ez az jó naponta** a **col2**. Kattintson a pipa jelre.

![Adja meg adatok](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Látni fogja hasonlót

![minta-kimenet](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Mintakód
Egy másik módszer a RR-EKET az Ügyfélkód származik. Ha **kérelem/válasz** az irányítópultot és a felülről lefelé görgetési jelennek meg példakód C#, Python, és R. Ezenkívül megjelenik az RRS kérelem a kérelem URI-azonosítója, beleértve a szintaxist fejlécek és törzse.

Ez az útmutató bemutatja egy működő Python példa. Módosítani kell a **munkaterület**, **szolgáltatás**, és **api_key** a kísérlet.

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

#### <a name="test-bes-endpoint"></a>Teszt BES végpont
Kattintson a **kötegelt végrehajtás** az irányítópult és alján görgessen. Mintakód látni fogja a C#, Python, és R. A feladat elküldése, elindíthat egy feladatot, az állapot vagy a feladat eredményeinek lekérése és törli a feladatot BES kérelmeket szintaxist is megtekintheti.

Ez az útmutató bemutatja egy működő Python példa. Módosítani kell a **munkaterület**, **szolgáltatás**, és **api_key** a kísérlet. Továbbá, hogy módosítania kell a **tárfióknév**, **tárfiók kulcsa**, és **storage-tároló nevének**. Végül, szüksége lesz a helyét, módosítsa a **bemeneti fájl** és helye a **kimeneti fájl**.

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
        # If you are using Python 3+, replace urllib2 with urllib.request in the follwing code
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
