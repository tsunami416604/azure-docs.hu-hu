---
title: "U-SQL parancsfájl - Azure használatával adatok átalakítása |} Microsoft Docs"
description: "Ismerje meg, hogyan kell feldolgozni vagy átalakítási adatok számítási szolgáltatás Azure Data Lake Analytics U-SQL-parancsfájlok futtatásával."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: 7800329e7f56d604c7911d3997fa76a0fac91664
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Adatok átalakítása Azure Data Lake Analytics U-SQL-parancsfájlok futtatásával 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-usql-activity.md)
> * [2. verzió – Előzetes verzió](transform-data-using-data-lake-analytics.md)

Egy folyamatot egy az Azure data factory az adatokat a csatolt tárolószolgáltatások csatolt számítási szolgáltatások használatával dolgozza fel. Ha minden tevékenység egyedi feldolgozása műveletet hajt végre tevékenységek sorrendje tartalmaz. Ez a cikk ismerteti a **Data Lake Analytics U-SQL tevékenység** , amelyen fut a **U-SQL** a parancsfájl egy **Azure Data Lake Analytics** számítási kapcsolódó szolgáltatás. 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [V1 USQL tevékenység](v1/data-factory-usql-activity.md).

Azure Data Lake Analytics-fiók létrehozása előtt hoz létre egy folyamatot egy Data Lake Analytics U-SQL-tevékenység. Azure Data Lake Analytics kapcsolatos további tudnivalókért lásd: [Ismerkedés az Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics társított szolgáltatás
Létrehozhat egy **Azure Data Lake Analytics** társított szolgáltatás az Azure Data Lake Analytics csatolásához számítási egy az Azure data factory szolgáltatást. A Data Lake Analytics U-SQL-tevékenység a feldolgozási szolgáltatásnak hivatkozik. 

A következő táblázat ismerteti a JSON-definícióból használt általános tulajdonságok. 

| Tulajdonság                 | Leírás                              | Szükséges                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **típusa**                 | A type tulajdonságot kell megadni: **AzureDataLakeAnalytics**. | Igen                                      |
| **Fióknév**          | Az Azure Data Lake Analytics-fiók neve.  | Igen                                      |
| **dataLakeAnalyticsUri** | Az Azure Data Lake Analytics URI.           | Nem                                       |
| **subscriptionId**       | Azure subscription ID                    | Nem (Ha nincs megadva, a data factory-előfizetése szerepel). |
| **resourceGroupName**    | Azure erőforráscsoport-név                | Nem (Ha nincs megadva, az adat-előállító erőforráscsoport szerepel). |

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése
Az Azure Data Lake Analytics kapcsolódó szolgáltatás egy szolgáltatás egyszerű hitelesítés az Azure Data Lake Analytics szolgáltatáshoz való kapcsolódáshoz szükséges. Szolgáltatás egyszerű hitelesítést használ, egy alkalmazás entitás regisztrálni kell az Azure Active Directory (Azure AD), és a Data Lake Analytics és a Data Lake Store használ hozzáférést engedélyez. Részletes útmutató: [szolgáltatások közötti hitelesítési](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Jegyezze fel a következő érték, melynek segítségével határozza meg a társított szolgáltatás:
* Alkalmazásazonosító
* Alkalmazás kulcs 
* Bérlőazonosító

Szolgáltatás egyszerű hitelesítés használatára a következő tulajdonságok megadásával:

| Tulajdonság                | Leírás                              | Szükséges |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Adja meg az alkalmazás ügyfél-azonosítót.     | Igen      |
| **servicePrincipalKey** | Adja meg az alkalmazás kulcsot.           | Igen      |
| **Bérlői**              | Adja meg a bérlői adatokat (tartomány nevét vagy a bérlő azonosító) alatt az alkalmazás található. Azt az Azure-portál jobb felső sarkában az egér rámutató által kérheti le. | Igen      |

**Példa: Szolgáltatás egyszerű hitelesítés**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

A társított szolgáltatás kapcsolatos további információkért lásd: [összekapcsolt szolgáltatások számítási](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-tevékenység
A következő JSON-részlet egy folyamatot, egy Data Lake Analytics U-SQL tevékenység határozza meg. A tevékenység definíciójának van a korábban létrehozott Azure Data Lake Analytics társított szolgáltatás hivatkozása. A Data Lake Analytics U-SQL parancsfájl végrehajtására, adat-előállító küldi el a parancsfájl a megadott a Data Lake Analytics és a szükséges bemenetekhez és kimenetekhez definiálva van a Data Lake Analytics beolvasása és a kimeneti parancsfájlt. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "AzureDataLakeAnalyticsLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "LinkedServiceofAzureBlobStorageforscriptPath",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

A következő táblázat ismerteti a neveket és leírásokat erre a tevékenységre jellemző tulajdonságok. 

| Tulajdonság            | Leírás                              | Szükséges |
| :------------------ | :--------------------------------------- | :------- |
| név                | A tevékenység a folyamat neve.     | Igen      |
| leírás         | A tevékenység mit leíró szöveg.  | Nem       |
| type                | Data Lake Analytics U-SQL-tevékenység, a tevékenység típusa nem **DataLakeAnalyticsU-SQL**. | Igen      |
| linkedServiceName   | Az Azure Data Lake Analytics társított szolgáltatás. A csatolt szolgáltatással kapcsolatos további tudnivalókért lásd: [összekapcsolt szolgáltatások számítási](compute-linked-services.md) cikk.  |Igen       |
| scriptPath          | A U-SQL parancsfájlt tartalmazó mappa elérési útja. A fájl neve nem kis-és nagybetűket. | Igen      |
| scriptLinkedService | Kapcsolódó szolgáltatás, amely a tárolóban, amely tartalmazza az adat-előállító parancsfájl | Igen      |
| degreeOfParallelism | A feladat futtatásához egyidejűleg használt csomópontok maximális száma. | Nem       |
| prioritás            | Azt határozza meg, melyet futtatni kíván szereplő várólistáján szereplő feladatok közül melyeket. Az alacsonyabb a szám, annál magasabb a prioritás. | Nem       |
| paraméterek          | A U-SQL parancsfájl paraméterek          | Nem       |
| runtimeVersion      | A U-SQL motort használja futásidejű verzióját | Nem       |
| compilationMode     | <p>Fordítási mód az U-SQL. A következő értékek egyike lehet: **Szemantikus:** csak a szemantikai ellenőrzések és a szükséges megerősítések, képes **teljes:** hajtsa végre a teljes fordítási, beleértve a szintaxis-ellenőrzés, optimalizálás, kódgenerálás, stb., **SingleBox:** hajtsa végre a teljes fordítási SingleBox való a TargetType beállítással. Ez a tulajdonság értékét nem adja meg, ha a kiszolgáló meghatározza a optimális fordítás módja. | Nem |

Adat-előállító küldi el a további részletekért lásd [SearchLogProcessing.txt parancsfájl Definition](#sample-u-sql-script) a parancsfájl definíciójának. 

## <a name="sample-u-sql-script"></a>Minta U-SQL parancsfájl

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
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

A fenti mintaparancsfájl, a bemeneti és kimeneti parancsfájlt definiálva van  **@in**  és  **@out**  paraméterek. Az értékek  **@in**  és  **@out**  a U-SQL parancsfájl átadott paraméterek dinamikusan által Data Factory "parameters" szakaszában. 

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

## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikkekben talál, amely ismerteti az adatok más módon: 

* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [A Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce művelethez](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streamelési tevékenységben](transform-data-using-hadoop-streaming.md)
* [A Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [A tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
