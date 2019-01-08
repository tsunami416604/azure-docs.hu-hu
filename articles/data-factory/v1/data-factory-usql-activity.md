---
title: Adatok átalakítása a használatával a U-SQL-szkript – Azure |} A Microsoft Docs
description: Ismerje meg, feldolgozása és átalakítási adatok Azure Data Lake Analytics számítási szolgáltatás az U-SQL-parancsfájlok futtatásával.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: douglasl
robots: noindex
ms.openlocfilehash: 7631b103d6d14cceb2c320d56e9f68d9ea57e4d8
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020846"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Adatok átalakítása az Azure Data Lake Analytics U-SQL-parancsfájlok futtatásával 
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-usql-activity.md)
> * [2-es verzió (aktuális verzió)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [U-SQL-tevékenység a v2-ben](../transform-data-using-data-lake-analytics.md).

Az Azure data factory-folyamatot az adatok az társított storage-szolgáltatásokban csatolt számítási szolgáltatások használatával dolgozza fel. A tevékenységek, ahol minden egyes tevékenységhez meghatározott feldolgozási műveletet hajt végre egy feladatütemezési tartalmazza. Ez a cikk ismerteti a **Data Lake Analytics U-SQL-tevékenység** futtat egy **U-SQL** a parancsfájl egy **Azure Data Lake Analytics** számítási társított szolgáltatás. 

Hozzon létre egy Azure Data Lake Analytics-fiókot egy Data Lake Analytics U-SQL-tevékenység a folyamat létrehozása előtt. Az Azure Data Lake Analytics kapcsolatos további információkért lásd: [Ismerkedés az Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Tekintse át a [hozhat létre az első folyamat oktatóanyag](data-factory-build-your-first-pipeline.md) részletes lépései hozzon létre egy adat-előállítót, társított szolgáltatások, adatkészletek és folyamatok. Data Factory Editor vagy a Visual Studio vagy az Azure PowerShell-kódrészletek JSON használatával a Data Factory-entitások létrehozása.

## <a name="supported-authentication-types"></a>Támogatott hitelesítési típusok
U-SQL-tevékenység hitelesítési típusok ellen a Data Lake Analytics alábbi támogatja:
* Egyszerű szolgáltatásnév hitelesítése
* Felhasználói hitelesítő adatok (OAuth) hitelesítés 

Azt javasoljuk, hogy használjon egyszerű szolgáltatásnév hitelesítése, különösen egy ütemezett U-SQL-végrehajtás. Jogkivonat lejárati fordulhat elő a felhasználói hitelesítő adatok hitelesítéssel. További konfigurációs információkért lásd: a [társított szolgáltatások tulajdonságai](#azure-data-lake-analytics-linked-service) szakaszban.

## <a name="azure-data-lake-analytics-linked-service"></a>Az Azure Data Lake Analytics-beli társított szolgáltatást
Létrehoz egy **Azure Data Lake Analytics** társított szolgáltatást, az Azure Data Lake Analytics számítási szolgáltatás az Azure data factoryt. A Data Lake Analytics U-SQL-tevékenység, a folyamat a társított szolgáltatásra vonatkozik. 

Az alábbi táblázat ismerteti a JSON-definíciójában használt általános tulajdonságokat. További választhat egyszerű szolgáltatás és a felhasználói hitelesítő adatok hitelesítése.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| **type** |A type tulajdonságot kell beállítani: **AzureDataLakeAnalytics**. |Igen |
| **Fióknév** |Az Azure Data Lake Analytics-fiók neve. |Igen |
| **dataLakeAnalyticsUri** |Az Azure Data Lake Analytics URI. |Nem |
| **subscriptionId** |Azure-előfizetés azonosítója |Nem (Ha nincs megadva, a data Factory előfizetés szerepel). |
| **resourceGroupName** |Azure-erőforráscsoport neve |Nem (Ha nincs megadva, a data Factory erőforrás-csoport szerepel). |

### <a name="service-principal-authentication-recommended"></a>Egyszerű szolgáltatásnév hitelesítése (ajánlott)
Egyszerű szolgáltatásnév hitelesítése alkalmazás entitás regisztrálása az Azure Active Directory (Azure AD), és hozzáférést, a Data Lake Store. Részletes lépéseiért lásd: [szolgáltatások közötti hitelesítés](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Jegyezze fel a következő értékeket, mert a társított szolgáltatás definiálásához használja:
* Alkalmazásazonosító
* Alkalmazáskulcs 
* Bérlőazonosító

Egyszerű szolgáltatásnév hitelesítése használja a következő tulajdonságok megadásával:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| **servicePrincipalId** | Adja meg az alkalmazás ügyfél-azonosítót. | Igen |
| **servicePrincipalKey** | Adja meg az alkalmazáskulcsot. | Igen |
| **bérlő** | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Kérheti a viszi az egérmutatót az Azure portal jobb felső sarkában. | Igen |

**Példa: Egyszerű szolgáltatásnév hitelesítése**
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
Másik lehetőségként használhatja felhasználói hitelesítő adatok hitelesítési Data Lake Analytics a következő tulajdonságok megadásával:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| **Engedélyezési** | Kattintson a **engedélyezés** gombra a Data Factory szerkesztőjében, és adja meg a hitelesítő adat, amelyet az automatikusan létrehozott engedélyezési URL-címet rendel hozzá ezt a tulajdonságot. | Igen |
| **sessionId** | OAuth munkamenet-azonosító az OAuth hitelesítési munkamenetből. Minden munkamenet-azonosító egyedi, és csak egyszer használhatók fel. Ez a beállítás automatikusan jön létre a Data Factory Editor használata esetén. | Igen |

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

#### <a name="token-expiration"></a>Jogkivonat lejáratáról
Az engedélyezési kód használatával létrehozott a **engedélyezés** gomb kis idő múlva jár le. Tekintse meg az alábbi táblázat a különböző típusú felhasználói fiókokat a lejárati idejét. Láthatja, hogy a következő hiba jelenik meg, ha a hitelesítés **jogkivonat lejár**: Hitelesítőadat-műveleti hiba: invalid_grant - AADSTS70002: Hiba történt a hitelesítő érvényesítésekor. AADSTS70008: A megadott hozzáférési engedély lejárt vagy visszavont. Nyomkövetési azonosító: d18629e8-af88-43c5-88e3-d8419eb1fca1 korrelációs azonosító: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 időbélyeg: 2015. 12 – 15 21:09:31Z

| Felhasználó típusa | Tétlenség után lejár |
|:--- |:--- |
| Felhasználói fiókok Azure Active Directory által nem felügyelt (@hotmail.com, @live.comstb.) |12 óra |
| Felhasználói fiókok felügyelt által az Azure Active Directory (AAD) |Futtassa az utolsó szelet 14 nappal. <br/><br/>90 nap, ha egy OAuth-alapú társított szolgáltatás alapján végezzen szeletelést, 14 naponta legalább egyszer futtatja. |

Ez a hiba elkerüléséhez/elhárítási, engedélyezze újra a használatával a **engedélyezés** gombra, ha a **jogkivonat lejár** és ismételt üzembe helyezése a társított szolgáltatás. Az értékeket is létrehozhat **sessionId** és **engedélyezési** tulajdonságait programozott módon a következő kódot:

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

Lásd: [AzureDataLakeStoreLinkedService osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), és [AuthorizationSessionGetResponse osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) témakörök tudnivalók a Data Factory-osztályokat használja a kód. Vegyen fel egy hivatkozást: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll WindowsFormsWebAuthenticationDialog osztály. 

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-tevékenység
A következő JSON-kódrészlet definiál egy Data Lake Analytics U-SQL-tevékenység rendelkező folyamatot. A tevékenység meghatározásában tartalmaz egy hivatkozást a korábban létrehozott Azure Data Lake Analytics hivatkozott szolgáltatást.   

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
| type                | A type tulajdonságot állítsa **DataLakeAnalyticsU-SQL**. | Igen                                      |
| linkedServiceName   | A Data Factory társított szolgáltatásként regisztrálva az Azure Data Lake Analytics mutató hivatkozás | Igen                                      |
| scriptPath          | A U-SQL parancsfájlt tartalmazó mappa elérési útja. A fájl neve, a kis-és nagybetűket. | Nem (Ha a parancsfájl használata)                   |
| scriptLinkedService | A tároló, amely tartalmazza a szkriptet az adat-előállító mutató társított szolgáltatás | Nem (Ha a parancsfájl használata)                   |
| parancsfájl              | Adja meg a beágyazott parancsfájlja scriptPath és a scriptLinkedService megadása helyett. Például: `"script": "CREATE DATABASE test"`. | Nem (Ha a scriptPath és a scriptLinkedService használ) |
| degreeOfParallelism | A feladat futtatásához egyidejűleg használt csomópontok maximális száma. | Nem                                       |
| prioritás            | Meghatározza, hogy mely feladatok közül, hogy a rendszer várólistára helyezi, melyet futtatni kíván van kiválasztva. Az alacsonyabb a szám, annál magasabb a prioritás. | Nem                                       |
| paraméterek          | A U-SQL-szkript paramétereit          | Nem                                       |
| runtimeVersion      | A U-SQL-motor használata futtatókörnyezet verziója | Nem                                       |
| compilationMode     | <p>Fordítási mód az U-SQL. Ezek az értékek egyikének kell lennie:</p> <ul><li>**Szemantikai:** Szemantikai ellenőrzési és a szükséges megerősítések csak végrehajtani.</li><li>**Teljes:** Hajtsa végre a teljes fordítás, beleértve a szintaxis-ellenőrzés, optimalizálás, generování kódu, stb.</li><li>**SingleBox:** Hajtsa végre a teljes fordítás SingleBox TargetType beállítással együtt.</li></ul><p>Ez a tulajdonság értékét nem adja meg, ha a kiszolgáló meghatározza, hogy az optimális fordítási mód. </p> | Nem                                       |

Lásd: [SearchLogProcessing.txt szkriptet definíciójának](#sample-u-sql-script) a parancsfájl-definíciójában. 

## <a name="sample-input-and-output-datasets"></a>Példa a bemeneti és kimeneti adatkészleteket
### <a name="input-dataset"></a>Bemeneti adatkészlet
Ebben a példában a bemeneti adatokat egy Azure Data Lake Store (searchlog.tsv fájlt fájlt a datalake/input mappában) található. 

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
Ebben a példában a U-SQL-parancsfájl által előállított kimeneti adatok tárolva az Azure Data Lake Store (datalake-/ kimeneti mappa). 

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

### <a name="sample-data-lake-store-linked-service"></a>Mintául szolgáló Data Lake Store társított szolgáltatás
Ez a definíció, a minta Azure Data Lake Store-beli társított szolgáltatást a bemeneti és kimeneti adatkészleteket használja. 

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

Lásd: [adatok importálására és az Azure Data Lake Store](data-factory-azure-datalake-connector.md) JSON-tulajdonságokkal leírását a cikk. 

## <a name="sample-u-sql-script"></a>Példa U-SQL parancsfájl

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

A tartozó értékeket **@in** és **@out** a U-SQL parancsfájl átadott paraméterek dinamikusan ADF "parameters" szakaszában. A folyamat definíciójában a "parameters" című szakaszában talál.

Megadhat más tulajdonságokat, például degreeOfParallelism és prioritását, valamint a feladatokhoz, amelyek az Azure Data Lake Analytics szolgáltatás futnak a folyamat definíciójában.

## <a name="dynamic-parameters"></a>Dinamikus paraméterek
A minta folyamatdefiníció és paraméterek vannak társítva változtatható értékekkel. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Használja helyette a dinamikus paraméterek lehetőség. Példa: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

Ebben az esetben a bemeneti fájlok továbbra is mértékének /datalake/input mappából, és a kimeneti fájlok jönnek létre a /datalake/output mappában. A fájlok nevei megkülönböztetik a dinamikus a szelet kezdési időpont alapján.  


