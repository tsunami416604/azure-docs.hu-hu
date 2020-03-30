---
title: Adatok átalakítása U-SQL parancsfájllal – Azure
description: Ismerje meg, hogyan dolgozhatja fel vagy alakíthatja át az adatokat U-SQL-parancsfájlok azure Data Lake Analytics számítási szolgáltatáson való futtatásával.
services: data-factory
documentationcenter: ''
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/01/2017
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: c6d3510dfdd02bf2eb07d656c706c44d895c582d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927904"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Adatok átalakítása az Azure Data Lake Analyticsben található U-SQL-szkriptek futtatásával 
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-usql-activity.md)
> * [2-es verzió (aktuális verzió)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [U-SQL Activity in V2 című témakört.](../transform-data-using-data-lake-analytics.md)

Az Azure-adat-előállító kúra az összekapcsolt tárolási szolgáltatásokban lévő adatokat összekapcsolt számítási szolgáltatások használatával dolgozza fel. Olyan tevékenységek sorozatát tartalmazza, ahol minden tevékenység egy adott feldolgozási műveletet hajt végre. Ez a cikk ismerteti a **Data Lake Analytics U-SQL tevékenység,** amely egy **U-SQL-parancsfájlt** futtat egy **Azure Data Lake Analytics** számítási csatolt szolgáltatás. 

Hozzon létre egy Azure Data Lake Analytics-fiókot, mielőtt létrehozna egy folyamatot a Data Lake Analytics U-SQL tevékenységgel. Az Azure Data Lake Analytics szolgáltatásról az [Azure Data Lake Analytics – Első lépések az Azure Data Lake Analytics szolgáltatással kapcsolatos további tudnivalók.](../../data-lake-analytics/data-lake-analytics-get-started-portal.md)

Tekintse át [az első folyamat oktatóanyagának részletes](data-factory-build-your-first-pipeline.md) lépéseit az adat-előállító, a csatolt szolgáltatások, az adatkészletek és a folyamat létrehozásához. A Data Factory-entitások létrehozásához használja a JSON-kódrészleteket a Data Factory Editor vagy a Visual Studio vagy az Azure PowerShell segítségével.

## <a name="supported-authentication-types"></a>Támogatott hitelesítési típusok
Az U-SQL tevékenység a Data Lake Analytics hitelesítési típusai alatt támogatott:
* Egyszerű szolgáltatásnév hitelesítése
* Felhasználói hitelesítő adatok (OAuth) hitelesítése 

Azt javasoljuk, hogy az egyszerű szolgáltatás hitelesítése, különösen az ütemezett U-SQL-végrehajtás. A jogkivonat elévülési viselkedése a felhasználói hitelesítő adatok hitelesítésével fordulhat elő. A konfigurációval kapcsolatos részleteket a [Csatolt szolgáltatás tulajdonságai](#azure-data-lake-analytics-linked-service) című szakaszban találja.

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics csatolt szolgáltatás
Hozzon létre egy **Azure Data Lake Analytics-alapú** szolgáltatást, amely egy Azure Data Lake Analytics-számítási szolgáltatást kapcsol össze egy Azure-adat-előállítóval. A Data Lake Analytics U-SQL tevékenység a folyamatban hivatkozik erre a csatolt szolgáltatás. 

Az alábbi táblázat a JSON-definícióban használt általános tulajdonságok leírását tartalmazza. További választhat az egyszerű szolgáltatás és a felhasználói hitelesítő adatok hitelesítése között.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| **Típus** |A típustulajdonságot a következőre kell állítani: **AzureDataLakeAnalytics**. |Igen |
| **Accountname** |Azure Data Lake Analytics-fiók neve. |Igen |
| **dataLakeAnalyticsUri** |Azure Data Lake Analytics URI. |Nem |
| **előfizetésazonosító** |Azure-előfizetés azonosítója |Nem (ha nincs megadva, az adat-előállító előfizetését használja a rendszer). |
| **resourceGroupName** |Azure-erőforráscsoport neve |Nem (ha nincs megadva, az adat-előállító erőforráscsoportját használja a rendszer). |

### <a name="service-principal-authentication-recommended"></a>Egyszerű szolgáltatáshitelesítés (ajánlott)
A szolgáltatás egyszerű hitelesítés, regisztráljon egy alkalmazás entitás az Azure Active Directoryban (Azure AD), és adja meg a hozzáférést a Data Lake Store. A részletes lépéseket a [Szolgáltatás-szolgáltatás hitelesítése című témakörben található.](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md) Jegyezze fel a következő értékeket, amelyek segítségével definiálja a csatolt szolgáltatást:
* Alkalmazásazonosító
* Alkalmazáskulcs 
* Bérlőazonosító

A szolgáltatásegyszerű hitelesítés használata a következő tulajdonságok megadásával:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| **servicePrincipalId** | Adja meg az alkalmazás ügyfélazonosítóját. | Igen |
| **servicePrincipalKey** | Adja meg az alkalmazás kulcsát. | Igen |
| **Bérlő** | Adja meg a bérlői adatokat (tartománynév vagy bérlőazonosító), amely alatt az alkalmazás található. Az egér relamás, az Azure Portal jobb felső sarkában lévő egérrel érhető el. | Igen |

**Példa: Egyszerű szolgáltatáshitelesítés**
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
Másik lehetőségként használhatja a felhasználói hitelesítő adatok hitelesítését a Data Lake Analytics szolgáltatáshoz a következő tulajdonságok megadásával:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| **Engedélyezési** | Kattintson a Data Factory Editor **Engedélyezés** gombjára, és adja meg az automatikusan létrehozott engedélyezési URL-címet hozzárendelt hitelesítő adatait ehhez a tulajdonsághoz. | Igen |
| **Munkamenet** | OAuth munkamenet-azonosító az OAuth engedélyezési munkamenetből. Minden munkamenet-azonosító egyedi, és csak egyszer használható. Ez a beállítás automatikusan létrejön a Data Factory Editor használatakor. | Igen |

**Példa: Felhasználói hitelesítő adatok hitelesítése**
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

#### <a name="token-expiration"></a>Token lejárata
Az **Engedélyezés** gombbal létrehozott engedélyezési kód valamikor lejár. A különböző típusú felhasználói fiókok lejárati idejét az alábbi táblázatban láthatja. A hitelesítési **jogkivonat lejártakor**a következő hibaüzenet jelenhet meg: Hitelesítő adatok működési hibája: invalid_grant - AADSTS70002: Hiba a hitelesítő adatok érvényesítése közben. AADSTS70008: A megadott hozzáférési támogatás lejárt vagy visszavonásra került. Nyomazonosító: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelációs azonosító: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Időbélyeg: 2015-12-15 21:09:31Z

| Felhasználó típusa | Lejárat után |
|:--- |:--- |
| Nem az Azure Active Directory@hotmail.com @live.com( , , stb.) által kezelt felhasználói fiókok |12 óra |
| Az Azure Active Directory (AAD) által kezelt felhasználói fiókok |14 nappal az utolsó szelet futtatása után. <br/><br/>90 nap, ha egy oauth-alapú csatolt szolgáltatáson alapuló szelet 14 naponta legalább egyszer fut. |

A hiba elkerülése/megoldása érdekében engedélyezze újra az **Engedélyezés** gombot, amikor a **jogkivonat lejár,** és telepítse újra a csatolt szolgáltatást. A **munkamenet-azonosító** és az **engedélyezési** tulajdonságok értékeit a következő kód használatával is létrehozhatja programozott módon:

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

A kódban használt Data Factory-osztályokról az [AzureDataLakeStoreLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [az AzureDataLakeAnalyticsLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)és a [AuthorizationSessionGetResponse osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) témaköreit találja. Hivatkozás hozzáadása: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll a WindowsFormsWebAuthenticationDialog osztályhoz. 

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-tevékenység
A következő JSON-kódrészlet egy Data Lake Analytics U-SQL tevékenységgel rendelkező folyamatot határoz meg. A tevékenységdefiníció hivatkozást tartalmaz az Azure Data Lake Analytics korábban létrehozott kapcsolódó szolgáltatásra.   

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

Az alábbi táblázat a tevékenységre jellemző tulajdonságok nevét és leírását ismerteti. 

| Tulajdonság            | Leírás                              | Kötelező                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| type                | A típustulajdonságot **DataLakeAnalyticsU-SQL**-re kell állítani. | Igen                                      |
| linkedServiceName   | Hivatkozás a Data Factory ban összekapcsolt szolgáltatásként regisztrált Azure Data Lake Analytics szolgáltatásra | Igen                                      |
| scriptPath          | Az U-SQL parancsfájlt tartalmazó mappa elérési útja. A fájl neve nem imitot jelent a kis- és nagybetűk között. | Nem (parancsfájl használata esetén)                   |
| parancsfájlLinkedService | Csatolt szolgáltatás, amely a parancsfájlt tartalmazó tárolót az adat-előállítóhoz kapcsolja | Nem (parancsfájl használata esetén)                   |
| szkriptet.              | A parancsfájlPath és a scriptLinkedService megadása helyett adja meg a szövegközi parancsfájlt. Például: `"script": "CREATE DATABASE test"`. | Nem (ha scriptPath és scriptLinkedService) |
| fokOfParallelism | A feladat futtatásához egyidejűleg használt csomópontok maximális száma. | Nem                                       |
| Prioritás            | Azt határozza meg, hogy a várólistán lévő feladatok közül mely feladatokat kell először futtatni. Minél alacsonyabb a szám, annál magasabb a prioritás. | Nem                                       |
| paraméterek          | Az U-SQL parancsfájl paraméterei          | Nem                                       |
| runtimeVersion      | Az U-SQL motor használandó futásidejű verziója | Nem                                       |
| compilationMode     | <p>Az U-SQL fordítási módja. Az alábbi értékek valamelyikének kell lennie:</p> <ul><li>**Szemantikai:** Csak szemantikai ellenőrzéseket és szükséges józansági ellenőrzéseket végezzen.</li><li>**Teljes:** Végezze el a teljes összeállítást, beleértve a szintaxis ellenőrzését, optimalizálását, kódgenerálását stb.</li><li>**SingleBox:** Végezze el a teljes fordítást a TargetType beállítással a SingleBox értékre.</li></ul><p>Ha nem ad meg értéket ehhez a tulajdonsághoz, a kiszolgáló határozza meg az optimális fordítási módot. </p> | Nem                                       |

A parancsfájldefiníciót lásd: [SearchLogProcessing.txt parancsfájldefiníció.](#sample-u-sql-script) 

## <a name="sample-input-and-output-datasets"></a>Mintabemeneti és kimeneti adatkészletek
### <a name="input-dataset"></a>Bemeneti adatkészlet
Ebben a példában a bemeneti adatok egy Azure Data Lake Store -ban találhatók (SearchLog.tsv fájl a datalake/input mappában). 

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
Ebben a példában az U-SQL-parancsfájl által létrehozott kimeneti adatok egy Azure Data Lake Store (datalake/output mappa) tárolja. 

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

### <a name="sample-data-lake-store-linked-service"></a>Minta adattó tároló hozadékszolgáltatás
Itt a definíció a minta Azure Data Lake Store csatolt szolgáltatás a bemeneti/kimeneti adatkészletek által használt. 

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

A JSON-tulajdonságok leírását az [Adatok áthelyezése az Azure Data Lake Store áruházba és az Azure Data Lake Store-ból](data-factory-azure-datalake-connector.md) való áthelyezése című cikkben olvashat. 

## <a name="sample-u-sql-script"></a>Példa az U-SQL parancsfájlra

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

Az U-SQL ** \@** parancsfájl ** \@bejövő** és kimenő paramétereinek értékeit az ADF dinamikusan továbbítja a "paraméterek" szakasz használatával. Tekintse meg a "paraméterek" szakaszt a folyamat definíciójában.

Megadhatja más tulajdonságok, például degreeOfParallelism és prioritás, valamint a folyamat definíciója az Azure Data Lake Analytics szolgáltatásfutó feladatok.

## <a name="dynamic-parameters"></a>Dinamikus paraméterek
A mintafolyamat definíciójában a be- és kijelentkezési paraméterek kódolt értékekkel vannak hozzárendelve. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Lehetőség van dinamikus paraméterek használatára. Példa: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

Ebben az esetben a bemeneti fájlok továbbra is a /datalake/input mappából kerülnek felvételre, és a kimeneti fájlok a /datalake/output mappában jönnek létre. A fájlnevek dinamikusak a szelet kezdési időpontjától függően.  


