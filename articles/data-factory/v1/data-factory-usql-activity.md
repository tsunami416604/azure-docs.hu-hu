---
title: "U-SQL parancsfájl - Azure használatával adatok átalakítása |} Microsoft Docs"
description: "Ismerje meg, hogyan kell feldolgozni vagy átalakítási adatok számítási szolgáltatás Azure Data Lake Analytics U-SQL-parancsfájlok futtatásával."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 3a0a097afa0ef5efe11cb5044bf9ea5d399e463f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Adatok átalakítása Azure Data Lake Analytics U-SQL-parancsfájlok futtatásával 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](data-factory-usql-activity.md)
> * [2. verzió – Előzetes verzió](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [V2 U-SQL tevékenység](../transform-data-using-data-lake-analytics.md).

Egy folyamatot egy az Azure data factory az adatokat a csatolt tárolószolgáltatások csatolt számítási szolgáltatások használatával dolgozza fel. Ha minden tevékenység egyedi feldolgozása műveletet hajt végre tevékenységek sorrendje tartalmaz. Ez a cikk ismerteti a **Data Lake Analytics U-SQL tevékenység** , amelyen fut a **U-SQL** a parancsfájl egy **Azure Data Lake Analytics** számítási kapcsolódó szolgáltatás. 

Azure Data Lake Analytics-fiók létrehozása előtt hoz létre egy folyamatot egy Data Lake Analytics U-SQL-tevékenység. Azure Data Lake Analytics kapcsolatos további tudnivalókért lásd: [Ismerkedés az Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Tekintse át a [az adatcsatorna első oktatóanyaga Szerkesztés](data-factory-build-your-first-pipeline.md) egy adat-előállító létrehozásának részletes lépései a kapcsolódó szolgáltatások, az adatkészletek és a folyamat. A Data Factory Editor vagy a Visual Studio vagy az Azure PowerShell JSON kódtöredékek segítségével adat-előállító entitásokat hozhatnak létre.

## <a name="supported-authentication-types"></a>Támogatott hitelesítési típusok
U-SQL-tevékenység alábbi Data Lake Analytics elleni hitelesítési típust támogatja:
* Egyszerű szolgáltatásnév hitelesítése
* Felhasználói hitelesítő adatok (OAuth) hitelesítés 

Azt javasoljuk, hogy használja-e szolgáltatás egyszerű hitelesítést, különösen a egy ütemezett U-SQL-végrehajtást. Jogkivonat lejáratáról fordulhat elő a felhasználói hitelesítő adatok hitelesítéssel. További konfigurációs információkért lásd: a [szolgáltatástulajdonságok kapcsolódó](#azure-data-lake-analytics-linked-service) szakasz.

## <a name="azure-data-lake-analytics-linked-service"></a>Az Azure Data Lake Analytics társított szolgáltatás
Létrehozhat egy **Azure Data Lake Analytics** társított szolgáltatás az Azure Data Lake Analytics csatolásához számítási egy az Azure data factory szolgáltatást. A Data Lake Analytics U-SQL-tevékenység a feldolgozási szolgáltatásnak hivatkozik. 

A következő táblázat ismerteti a JSON-definícióból használt általános tulajdonságok. További választhat egyszerű szolgáltatásnév és felhasználói hitelesítő adatok hitelesítése.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| **típusa** |A type tulajdonságot kell megadni: **AzureDataLakeAnalytics**. |Igen |
| **Fióknév** |Az Azure Data Lake Analytics-fiók neve. |Igen |
| **datalakeanalyticsuri paraméter** |Az Azure Data Lake Analytics URI. |Nem |
| **előfizetés-azonosító** |Az Azure előfizetés-azonosító |Nem (Ha nincs megadva, a data factory-előfizetése szerepel). |
| **erőforráscsoport-név** |Azure erőforráscsoport-név |Nem (Ha nincs megadva, az adat-előállító erőforráscsoport szerepel). |

### <a name="service-principal-authentication-recommended"></a>Szolgáltatás egyszerű hitelesítés (ajánlott)
Szolgáltatás egyszerű hitelesítést használ, egy alkalmazás entitás regisztrálni kell az Azure Active Directory (Azure AD), és hozzáférést, a Data Lake Store-bA. Részletes útmutató: [szolgáltatások közötti hitelesítési](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Jegyezze fel a következő érték, melynek segítségével határozza meg a társított szolgáltatás:
* Alkalmazásazonosító
* Alkalmazás kulcs 
* Bérlőazonosító

Szolgáltatás egyszerű hitelesítés használatára a következő tulajdonságok megadásával:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| **servicePrincipalId** | Adja meg az alkalmazás ügyfél-azonosítót. | Igen |
| **servicePrincipalKey** | Adja meg az alkalmazás kulcsot. | Igen |
| **Bérlői** | Adja meg a bérlői adatokat (tartomány nevét vagy a bérlő azonosító) alatt az alkalmazás található. Azt az Azure-portál jobb felső sarkában az egér rámutató által kérheti le. | Igen |

**Példa: Szolgáltatás egyszerű hitelesítés**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Felhasználói hitelesítő adatok hitelesítése
Alternatív megoldásként használható felhasználói hitelesítő Data Lake Analytics megadását követően az alábbi tulajdonságokat:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| **engedélyezési** | Kattintson a **engedélyezés** a Data Factory Editor gombra, és adja meg a hitelesítő adatok, amelyek az automatikusan létrehozott engedélyezési URL-címet rendel hozzá ehhez a tulajdonsághoz. | Igen |
| **munkamenet-azonosító** | OAuth munkamenet-azonosító az OAuth hitelesítési munkamenetből. Minden munkamenet-azonosító egyedi, és csak egyszer használható. Ez a beállítás automatikusan jön létre, a Data Factory Editor használatakor. | Igen |

**Példa: Felhasználók hitelesítő adatok hitelesítése**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Jogkivonat lejáratáról
Az engedélyezési kód használatával előállított a **engedélyezés** gomb némi várakozás után lejár. Lásd az alábbi táblázatban a különböző típusú felhasználói fiókokat a lejárati idejét. Jelenhetnek meg a következő hiba jelenik meg, ha a hitelesítési **-token érvényessége lejár**: hitelesítőadat-műveleti hiba: invalid_grant - AADSTS70002: Hiba történt a hitelesítő adatok ellenőrzése. AADSTS70008: A megadott hozzáférés biztosítása lejárt vagy visszavonták. Nyomkövetési azonosító: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelációazonosító: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 időbélyegző: 2015-12-15 21:09:31Z

| Felhasználó típusa | Után lejár |
|:--- |:--- |
| Felhasználói fiókok Azure Active Directory által nem kezelt (@hotmail.com, @live.comstb.) |12 óra |
| Felhasználói fiókok felügyelete által Azure Active Directory (AAD) |Futtassa a 14 nap után utolsó újrapróbálása. <br/><br/>90 nap, ha a szelet OAuth-alapú társított szolgáltatás fut legalább 14 naponta. |

Ez a hiba elkerüléséhez/hárítsa, ismét engedélyezheti a használatával a **engedélyezés** gombra kattint, ha a **jogkivonat lejár** és telepítse újra a társított szolgáltatás. Értékek is létrehozhat **sessionId** és **engedélyezési** programozott módon, az alábbiak szerint kód tulajdonságok:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Lásd: [AzureDataLakeStoreLinkedService osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), és [AuthorizationSessionGetResponse osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) témakörök a Data Factory osztályok, a kódban használt vonatkozó további információért. Adjon hozzá egy hivatkozást,: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll WindowsFormsWebAuthenticationDialog osztálynál. 

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-tevékenység
A következő JSON-részlet egy folyamatot, egy Data Lake Analytics U-SQL tevékenység határozza meg. A tevékenység definíciójának van a korábban létrehozott Azure Data Lake Analytics társított szolgáltatás hivatkozása.   

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00Z",
        "end": "2015-08-08T01:00:00Z",
        "isPaused": false
    }
}
```

A következő táblázat ismerteti a neveket és leírásokat erre a tevékenységre jellemző tulajdonságok. 

| Tulajdonság            | Leírás                              | Szükséges                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| type                | A type tulajdonságot meg kell **DataLakeAnalyticsU-SQL**. | Igen                                      |
| linkedServiceName   | A Data Factory kapcsolt szolgáltatásként regisztrálva az Azure Data Lake Analytics mutató hivatkozás | Igen                                      |
| scriptPath          | A U-SQL parancsfájlt tartalmazó mappa elérési útja. A fájl neve nem kis-és nagybetűket. | Nem (Ha a parancsfájl használata)                   |
| scriptLinkedService | Kapcsolódó szolgáltatás, amely a tárolóban, amely tartalmazza az adat-előállító parancsfájl | Nem (Ha a parancsfájl használata)                   |
| Parancsfájl              | Adja meg a beágyazott parancsfájlja scriptPath és a scriptLinkedService megadása helyett. Például: `"script": "CREATE DATABASE test"`. | Nem (Ha a ScriptPath tulajdonságot is és a scriptLinkedService használ) |
| degreeOfParallelism | A feladat futtatásához egyidejűleg használt csomópontok maximális száma. | Nem                                       |
| Prioritás            | Azt határozza meg, melyet futtatni kíván szereplő várólistáján szereplő feladatok közül melyeket. Az alacsonyabb a szám, annál magasabb a prioritás. | Nem                                       |
| paraméterek          | A U-SQL parancsfájl paraméterek          | Nem                                       |
| runtimeVersion      | A U-SQL motort használja futásidejű verzióját | Nem                                       |
| compilationMode     | <p>Fordítási mód az U-SQL. A következő értékek egyike lehet:</p> <ul><li>**Szemantikus:** csak a szemantikai ellenőrzések és a szükséges megerősítések végrehajtani.</li><li>**Teljes:** hajtsa végre a teljes fordítási, beleértve a szintaxis-ellenőrzés, optimalizálás, kódgenerálás, stb.</li><li>**SingleBox:** hajtsa végre a teljes fordítási SingleBox való a TargetType beállítással.</li></ul><p>Ez a tulajdonság értékét nem adja meg, ha a kiszolgáló meghatározza a optimális fordítás módja. </p> | Nem                                       |

Lásd: [SearchLogProcessing.txt parancsfájl Definition](#sample-u-sql-script) a parancsfájl definíciójának. 

## <a name="sample-input-and-output-datasets"></a>Minta bemeneti és kimeneti adatkészletek
### <a name="input-dataset"></a>Bemeneti adatkészlet
Ebben a példában a bemeneti adatok az Azure Data Lake Store (searchlog.tsv fájl fájl a datalake-/ bemeneti mappában) található. 

```json
{
    "name": "DataLakeTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}    
```

### <a name="output-dataset"></a>Kimeneti adatkészlet
Ebben a példában a U-SQL parancsfájl által előállított kimeneti adatokat az Azure Data Lake Store (datalake-/ kimeneti mappa) tárolódik. 

```json
{
    "name": "EventsByRegionTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="sample-data-lake-store-linked-service"></a>A minta Data Lake Store társított szolgáltatás
Ez a minta Azure Data Lake Store társított szolgáltatás által a bemeneti/kimeneti adatkészletek használt definíciója. 

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
        }
    }
}
```

Lásd: [helyezze át az adatokat, és az Azure Data Lake Store](data-factory-azure-datalake-connector.md) cikk a JSON-tulajdonságok leírását. 

## <a name="sample-u-sql-script"></a>Minta U-SQL parancsfájl

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

Az értékek  **@in**  és  **@out**  a U-SQL parancsfájl átadott paraméterek dinamikusan ADF által a "parameters" szakaszában. A "parameters" című rész a csővezeték-definícióban.

Megadhat más tulajdonságait, például degreeOfParallelism és prioritását, valamint a csővezeték-definícióban az az Azure Data Lake Analytics szolgáltatásban futó feladatok.

## <a name="dynamic-parameters"></a>Dinamikus paraméterek
A minta-feldolgozási folyamat definícióban bejövő és kimenő adatforgalma paraméterek vannak hozzárendelve kódolt értékekkel. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Akkor használja helyette a dinamikus paraméterek lehet. Példa: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

Ebben az esetben a bemeneti fájlok továbbra is átveszik a /datalake/input mappából, és kimeneti fájlok jönnek létre, a /datalake/output mappában. Fájlnevek nem dinamikus szelet kezdési ideje alapján.  


