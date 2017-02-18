---
title: "A Data Lake Analytics használatának első lépései a REST API használatával | Microsoft Docs"
description: "Műveletek végrehajtása a Data Lake Analytics-en WebHDFS REST API-k használatával"
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 5e133d92-baaa-44c9-890c-ab2d85c91122
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/03/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2fea3686b1484406d31c5447c7d3d7e2451b827e
ms.openlocfilehash: 1898b3d6aa1a9ccbc9f4427cf994c02f9fa35abd


---
# <a name="get-started-with-azure-data-lake-analytics-using-rest-apis"></a>Az Azure Data Lake Analytics használatának első lépései REST API-k használatával
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Útmutató a Data Lake Analytics-fiókoknak, -feladatoknak és -katalógusnak a WebHDFS REST API-k és Data Lake Analytics REST API-k segítségével történő kezeléséhez. 

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Active Directory-alkalmazás létrehozása**. A Data Lake Analytics alkalmazás Azure AD-val történő hitelesítéséhez az Azure AD alkalmazást kell használni. Az Azure AD-val többféle módon is lehet hitelesíteni. Ezek a következők: **végfelhasználói hitelesítés** vagy **szolgáltatások közötti hitelesítés**. A hitelesítéssel kapcsolatos útmutatást és további információkat lásd: [Authenticate with Data Lake Analytics using Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md) (Hitelesítés a Data Lake Analytics-szel az Azure Active Directoryt használva).
* [cURL](http://curl.haxx.se/). Ez a cikk a cURL használatával mutatja be, hogyan lehet REST API-hívásokat indítani a Data Lake Analytics-fiókra.

## <a name="authenticate-with-azure-active-directory"></a>Hitelesítés az Azure Active Directoryval
Az Azure Active Directoryval kétféle módon lehet hitelesíteni.

### <a name="end-user-authentication-interactive"></a>Végfelhasználó hitelesítése (interaktív)
Ezzel a módszerrel az alkalmazás bejelentkezésre kéri a felhasználót, és minden művelet a felhasználó kontextusában lesz végrehajtva. 

Az interaktív hitelesítéshez kövesse az alábbi lépéseket:

1. Az alkalmazáson keresztül irányítsa át a felhasználót az alábbi URL-címre:
   
        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>
   
   > [!NOTE]
   > A \<REDIRECT-URI> értéket kódolni kell az URL-ben való használatra. A https://localhost esetében tehát használja a következőt: `https%3A%2F%2Flocalhost`)
   > 
   > 
   
    A jelen oktatóanyagban kicserélheti a fenti URL-ben szereplő helyőrző értékeket, és beillesztheti egy webböngésző címsorába. A rendszer átirányítja az Azure bejelentkezési azonosítójával történő hitelesítéshez. Miután sikeresen bejelentkezett, a válasz megjelenik a böngésző címsorában. A válasz az alábbi formátumban jelenik meg:
   
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>
2. Rögzítse a válaszban szereplő engedélyezési kódot. A jelen oktatóanyagban kimásolhatja a webböngésző címsorában szereplő engedélyezési kódot, majd a POST kérelemben továbbíthatja a jogkivonat végpontjához az alább látható módon:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>
   
   > [!NOTE]
   > Ebben az esetben a \<REDIRECT-URI> kódolása nem szükséges.
   > 
   > 
3. A válasz egy JSON-objektum, amely egy hozzáférési (pl. `"access_token": "<ACCESS_TOKEN>"`) és egy frissítési (pl. `"refresh_token": "<REFRESH_TOKEN>"`) jogkivonatot tartalmaz. Az alkalmazás a hozzáférési jogkivonatot az Azure Data Lake Store-hoz való hozzáféréshez, a frissítési jogkivonatot pedig egy új hozzáférési jogkivonat beszerzéséhez használja, amikor az előző lejár.
   
        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
4. Amikor a hozzáférési jogkivonat lejár, a frissítési jogkivonat használatával kérhet egy újat az alább látható módon:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<CLIENT-ID> \
             -F refresh_token=<REFRESH-TOKEN>

További információk az interaktív felhasználói hitelesítéssel kapcsolatban: [Authorization code grant flow](https://msdn.microsoft.com/library/azure/dn645542.aspx) (Az engedélyezési kód engedélyezési folyamata).

### <a name="service-to-service-authentication-non-interactive"></a>Szolgáltatások közötti hitelesítés (nem interaktív)
Ezzel a módszerrel az alkalmazás maga biztosítja saját hitelesítő adatait a műveletek végrehajtásához. Ehhez egy alábbihoz hasonló POST-kérelmet kell kiadnia: 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

A kérelem kimenete egy engedélyezési jogkivonatot fog tartalmazni (amelyet az alábbi kimenetben `access-token` jelöl), amelyet ezután a REST API-hívásokkal fog átadni. Mentse ezt az engedélyezési jogkivonatot egy szövegfájlba, mivel később még szüksége lesz rá a cikkben.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Ez a cikk a **nem interaktív** módszert alkalmazza. További információk a nem interaktív (szolgáltatások közötti) hívásokról: [Szolgáltatások közötti hívások hitelesítő adatok használatával](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="create-a-data-lake-analytics-account"></a>Data Lake Analytics-fiók létrehozása
Data Lake Analytics-fiók létrehozása előtt létre kell hoznia egy Azure-erőforráscsoportot és egy Data Lake Store-fiókot.  Lásd: [Data Lake Store-fiók létrehozása](../data-lake-store/data-lake-store-get-started-rest-api.md#create-a-data-lake-store-account).

A következő Curl-parancs egy fiók létrehozását mutatja be:

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<NewAzureDataLakeAnalyticsAccountName>?api-version=2016-11-01 -d@"C:\tutorials\adla\CreateDataLakeAnalyticsAccountRequest.json"

Cserélje le a \<`REDACTED`\> változót az engedélyezési jogkivonatra, az \<`AzureSubscriptionID`\> változót a saját előfizetés-azonosítójára, az \<`AzureResourceGroupName`\> változót egy meglévő Azure-erőforráscsoport nevére és a \<`NewAzureDataLakeAnalyticsAccountName`\> változót egy új Data Lake Analytics-fiók nevére. A kérelem hasznos adatai ezen parancs esetében a **CreateDatalakeAnalyticsAccountRequest.json** fájlban találhatók, amely a fenti `-d` paraméterhez lett megadva. Az input.json fájl tartalmai az alábbiakhoz hasonlók:

    {  
        "location": "East US 2",  
        "name": "myadla1004",  
        "tags": {},  
        "properties": {  
            "defaultDataLakeStoreAccount": "my1004store",  
            "dataLakeStoreAccounts": [  
                {  
                    "name": "my1004store"  
                }     
            ]
        }  
    }  


## <a name="list-data-lake-analytics-accounts-in-a-subscription"></a>A Data Lake Analytics-fiókok felsorolása egy előfizetésben
A következő Curl-parancs bemutatja, hogyan kell felsorolni a fiókokat egy előfizetésben:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/providers/Microsoft.DataLakeAnalytics/Accounts?api-version=2016-11-01

Cserélje le a \<`REDACTED`\> változót az engedélyezési jogkivonatra, az \<`AzureSubscriptionID`\> változót pedig a saját előfizetés-azonosítójára. Az eredmény az alábbihoz hasonlóan fog kinézni:

    {
        "value": [
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla0831.azuredatalakeanalytics.net",
                "accountId": "21e74660-0941-4880-ae72-b143c2615ea9",
                "creationTime": "2016-09-01T12:49:12.7451428Z",
                "lastModifiedTime": "2016-09-01T12:49:12.7451428Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla0831rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla0831",
            "name": "myadla0831",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            },
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla1004.azuredatalakeanalytics.net",
                "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
                "creationTime": "2016-10-04T20:46:42.287147Z",
                "lastModifiedTime": "2016-10-04T20:46:42.287147Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
            "name": "myadla1004",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            }
        ]
    }

## <a name="get-information-about-a-data-lake-analytics-account"></a>Data Lake Analytics-fiókkal kapcsolatos információk beszerzése
A következő Curl-parancs bemutatja, hogyan kell információkat beszerezni egy fiókról:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>?api-version=2015-11-01

Cserélje le a \<`REDACTED`\> változót az engedélyezési jogkivonatra, az \<`AzureSubscriptionID`\> változót a saját előfizetés-azonosítójára, az \<`AzureResourceGroupName`\> változót egy meglévő Azure-erőforráscsoport nevére és a \<`DataLakeAnalyticsAccountName`\> változót egy meglévő Data Lake Analytics-fiók nevére. Az eredmény az alábbihoz hasonlóan fog kinézni:

    {
        "properties": {
            "defaultDataLakeStoreAccount": "my1004store",
            "dataLakeStoreAccounts": [
            {
                "properties": {
                "suffix": "azuredatalakestore.net"
                },
                "name": "my1004store"
            }
            ],
            "provisioningState": "Creating",
            "state": null,
            "endpoint": null,
            "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
            "creationTime": null,
            "lastModifiedTime": null
        },
        "location": "East US 2",
        "tags": {},
        "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
        "name": "myadla1004",
        "type": "Microsoft.DataLakeAnalytics/accounts"
    }

## <a name="list-data-lake-stores-of-a-data-lake-analytics-account"></a>Data Lake Analytics-fiókhoz tartozó Data Lake-tárolók felsorolása
A következő Curl-parancs bemutatja, hogyan kell felsorolni az egyes fiókokhoz tartozó Data Lake-tárolókat:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>/DataLakeStoreAccounts/?api-version=2016-11-01

Cserélje le a \<`REDACTED`\> változót az engedélyezési jogkivonatra, az \<`AzureSubscriptionID`\> változót a saját előfizetés-azonosítójára, az \<`AzureResourceGroupName`\> változót egy meglévő Azure-erőforráscsoport nevére és a \<`DataLakeAnalyticsAccountName`\> változót egy meglévő Data Lake Analytics-fiók nevére. Az eredmény az alábbihoz hasonlóan fog kinézni:

    {
        "value": [
            {
            "properties": {
                "suffix": "azuredatalakestore.net"
            },
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004/dataLakeStoreAccounts/my1004store",
            "name": "my1004store",
            "type": "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts"
            }
        ]
    }

## <a name="submit-u-sql-jobs"></a>U-SQL-feladatok küldése
A következő Curl-parancs egy U-SQL-feladat küldését mutatja be:

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs/<NewGUID>?api-version=2016-03-20-preview -d@"C:\tutorials\adla\SubmitADLAJob.json"

Cserélje le a \<`REDACTED`\> változót az engedélyezési jogkivonatra, a \<`DataLakeAnalyticsAccountName`\> változót pedig egy meglévő Data Lake Analytics-fiók nevére. A kérelem hasznos adatai ezen parancs esetében a **SubmitADLAJob.json** fájlban találhatók, amely a fenti `-d` paraméterhez lett megadva. Az input.json fájl tartalmai az alábbiakhoz hasonlók:

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "properties": {
            "type": "USql",
            "script": "@searchlog =\n    EXTRACT UserId          int,\n            Start           DateTime,\n            Region          string,\n            Query          
        string,\n            Duration        int?,\n            Urls            string,\n            ClickedUrls     string\n    FROM \"/Samples/Data/SearchLog.tsv\"\n    US
        ING Extractors.Tsv();\n\nOUTPUT @searchlog   \n    TO \"/Output/SearchLog-from-Data-Lake.csv\"\nUSING Outputters.Csv();"
        }
    }

Az eredmény az alábbihoz hasonlóan fog kinézni:

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "myadl@SPI",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "2016-10-05T13:54:59.9871859+00:00",
        "state": "Compiling",
        "result": "Succeeded",
        "stateAuditRecords": [
            {
            "newState": "New",
            "timeStamp": "2016-10-05T13:54:59.9871859+00:00",
            "details": "userName:myadl@SPI;submitMachine:N/A"
            }
        ],
        "properties": {
            "owner": "myadl@SPI",
            "resources": [],
            "runtimeVersion": "default",
            "rootProcessNodeId": "00000000-0000-0000-0000-000000000000",
            "algebraFilePath": "adl://myadls0831.azuredatalakestore.net/system/jobservice/jobs/Usql/2016/10/05/13/54/8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a/algebra.xml",
            "compileMode": "Semantic",
            "errorSource": "Unknown",
            "totalCompilationTime": "PT0S",
            "totalPausedTime": "PT0S",
            "totalQueuedTime": "PT0S",
            "totalRunningTime": "PT0S",
            "type": "USql"
        }
    }


## <a name="list-u-sql-jobs"></a>U-SQL-feladatok felsorolása
A következő Curl-parancs az U-SQL-feladatok felsorolását mutatja be:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs?api-version=2016-11-01 

Cserélje le a \<`REDACTED`\> változót az engedélyezési jogkivonatra, a \<`DataLakeAnalyticsAccountName`\> változót pedig egy meglévő Data Lake Analytics-fiók nevére. 

Az eredmény az alábbihoz hasonlóan fog kinézni:

    {
    "value": [
        {
        "jobId": "65cf1691-9dbe-43cd-90ed-1cafbfb406fb",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someone@microsoft.com",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:46:53 GMT",
        "startTime": "Wed, 05 Oct 2016 13:47:33 GMT",
        "endTime": "Wed, 05 Oct 2016 13:48:07 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        },
        {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someoneadl@SPI",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:54:59 GMT",
        "startTime": "Wed, 05 Oct 2016 13:55:43 GMT",
        "endTime": "Wed, 05 Oct 2016 13:56:11 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        }
    ],
    "nextLink": null,
    "count": null
    }


## <a name="get-catalog-items"></a>Katalóguselemek beolvasása
A következő Curl-parancs bemutatja, hogyan lehet beolvasni a katalógushoz tartozó adatbázisokat:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/catalog/usql/databases?api-version=2016-11-01

Az eredmény az alábbihoz hasonlóan fog kinézni:

    {
    "@odata.context":"https://myadla0831.azuredatalakeanalytics.net/sqlip/$metadata#databases","value":[
        {
        "computeAccountName":"myadla0831","databaseName":"mytest","version":"f6956327-90b8-4648-ad8b-de3ff09274ea"
        },{
        "computeAccountName":"myadla0831","databaseName":"master","version":"e8bca908-cc73-41a3-9564-e9bcfaa21f4e"
        }
    ]
    }

## <a name="see-also"></a>Lásd még:
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Analyze Website logs using Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) (Webhelyek naplóinak elemzése az Azure Data Lake Analytics használatával).
* Ismerkedés a U-SQL-alkalmazások fejlesztésével: [Develop U-SQL scripts using Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) (U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával).
* A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével).
* Felügyeleti feladatok: [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md) (Az Azure Data Lake Analytics kezelése az Azure Portallal).
* A Data Lake Analytics áttekintése: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).
* Ha ugyanezt az oktatóanyagot más eszközök használatával szeretné megtekinteni, kattintson az oldal tetején található lapválasztókra.
* A diagnosztikai információk naplózása: [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md) (Az Azure Data Lake Analytics diagnosztikai naplóinak elérése).




<!--HONumber=Feb17_HO1-->


