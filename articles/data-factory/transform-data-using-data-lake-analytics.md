---
title: Adatok átalakítása U-SQL parancsfájllal
description: Ismerje meg, hogyan dolgozhatja fel vagy alakíthatja át az adatokat U-SQL-parancsfájlok azure Data Lake Analytics számítási szolgáltatáson való futtatásával.
services: data-factory
documentationcenter: ''
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2018
ms.openlocfilehash: 257c71f7994b889540ec8cc5d0f384f3f8894f4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74913276"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Adatok átalakítása az Azure Data Lake Analyticsben található U-SQL-szkriptek futtatásával 
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-usql-activity.md)
> * [Aktuális verzió](transform-data-using-data-lake-analytics.md)

Az Azure-adat-előállító kúra az összekapcsolt tárolási szolgáltatásokban lévő adatokat összekapcsolt számítási szolgáltatások használatával dolgozza fel. Olyan tevékenységek sorozatát tartalmazza, ahol minden tevékenység egy adott feldolgozási műveletet hajt végre. Ez a cikk ismerteti a **Data Lake Analytics U-SQL tevékenység,** amely egy **U-SQL-parancsfájlt** futtat egy **Azure Data Lake Analytics** számítási csatolt szolgáltatás. 

Hozzon létre egy Azure Data Lake Analytics-fiókot, mielőtt létrehozna egy folyamatot a Data Lake Analytics U-SQL tevékenységgel. Az Azure Data Lake Analytics szolgáltatásról az [Azure Data Lake Analytics – Első lépések az Azure Data Lake Analytics szolgáltatással kapcsolatos további tudnivalók.](../data-lake-analytics/data-lake-analytics-get-started-portal.md)


## <a name="azure-data-lake-analytics-linked-service"></a>Az Azure Data Lake Analytics kapcsolt szolgáltatása
Hozzon létre egy **Azure Data Lake Analytics-alapú** szolgáltatást, amely egy Azure Data Lake Analytics-számítási szolgáltatást kapcsol össze egy Azure-adat-előállítóval. A Data Lake Analytics U-SQL tevékenység a folyamatban hivatkozik erre a csatolt szolgáltatás. 

Az alábbi táblázat a JSON-definícióban használt általános tulajdonságok leírását tartalmazza. 

| Tulajdonság                 | Leírás                              | Kötelező                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **Típus**                 | A típustulajdonságot a következőre kell állítani: **AzureDataLakeAnalytics**. | Igen                                      |
| **Accountname**          | Azure Data Lake Analytics-fiók neve.  | Igen                                      |
| **dataLakeAnalyticsUri** | Azure Data Lake Analytics URI.           | Nem                                       |
| **előfizetésazonosító**       | Azure-előfizetés azonosítója                    | Nem                                       |
| **resourceGroupName**    | Azure-erőforráscsoport neve                | Nem                                       |

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése
Az Azure Data Lake Analytics-alapú szolgáltatás egyszerű hitelesítést igényel az Azure Data Lake Analytics szolgáltatáshoz való csatlakozáshoz. A szolgáltatás egyszerű hitelesítés, regisztráljon egy alkalmazás entitás az Azure Active Directoryban (Azure AD), és megadja a hozzáférést mind a Data Lake Analytics és a Data Lake Store általa használt. A részletes lépéseket a [Szolgáltatás-szolgáltatás hitelesítése című témakörben található.](../data-lake-store/data-lake-store-authenticate-using-active-directory.md) Jegyezze fel a következő értékeket, amelyek segítségével definiálja a csatolt szolgáltatást:

* Alkalmazásazonosító
* Alkalmazáskulcs 
* Bérlőazonosító

A [Felhasználó hozzáadása varázslóval](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user)egyszerű szolgáltatást adhat az Azure Data Lake-nek.

A szolgáltatásegyszerű hitelesítés használata a következő tulajdonságok megadásával:

| Tulajdonság                | Leírás                              | Kötelező |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Adja meg az alkalmazás ügyfélazonosítóját.     | Igen      |
| **servicePrincipalKey** | Adja meg az alkalmazás kulcsát.           | Igen      |
| **Bérlő**              | Adja meg a bérlői adatokat (tartománynév vagy bérlőazonosító), amely alatt az alkalmazás található. Az egér relamás, az Azure Portal jobb felső sarkában lévő egérrel érhető el. | Igen      |

**Példa: Egyszerű szolgáltatáshitelesítés**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "<azure data lake analytics URI>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

Ha többet szeretne megtudni a csatolt szolgáltatásról, olvassa el a Csatolt szolgáltatások számítása című [témakört.](compute-linked-services.md)

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-tevékenység
A következő JSON-kódrészlet egy Data Lake Analytics U-SQL tevékenységgel rendelkező folyamatot határoz meg. A tevékenységdefiníció hivatkozást tartalmaz az Azure Data Lake Analytics korábban létrehozott kapcsolódó szolgáltatásra. A Data Lake Analytics U-SQL parancsfájl végrehajtásához a Data Factory elküldi a megadott parancsfájlt a Data Lake Analytics-nek, és a szükséges bemeneteket és kimeneteket a Data Lake Analytics lehívásához és kimenetéhez szükséges parancsfájl határozza meg. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "<linked service name of Azure Data Lake Analytics>",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "<linked service name of Azure Data Lake Store or Azure Storage which contains the U-SQL script>",
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

Az alábbi táblázat a tevékenységre jellemző tulajdonságok nevét és leírását ismerteti. 

| Tulajdonság            | Leírás                              | Kötelező |
| :------------------ | :--------------------------------------- | :------- |
| név                | A folyamatban lévő tevékenység neve     | Igen      |
| leírás         | A tevékenység tevékenységét leíró szöveg.  | Nem       |
| type                | A Data Lake Analytics U-SQL tevékenység esetén a tevékenység típusa **DataLakeAnalyticsU-SQL.** | Igen      |
| linkedServiceName   | Összekapcsolt szolgáltatás az Azure Data Lake Analytics szolgáltatáshoz. A csatolt szolgáltatásról a [Csatolt szolgáltatások számítási cikkében](compute-linked-services.md) olvashat.  |Igen       |
| scriptPath          | Az U-SQL parancsfájlt tartalmazó mappa elérési útja. A fájl neve nem imitot jelent a kis- és nagybetűk között. | Igen      |
| parancsfájlLinkedService | Összekapcsolt szolgáltatás, amely összekapcsolja az **Azure Data Lake Store-t** vagy az **Azure Storage-t,** amely a parancsfájlt tartalmazza az adat-előállítóval | Igen      |
| fokOfParallelism | A feladat futtatásához egyidejűleg használt csomópontok maximális száma. | Nem       |
| Prioritás            | Azt határozza meg, hogy a várólistán lévő feladatok közül mely feladatokat kell először futtatni. Minél alacsonyabb a szám, annál magasabb a prioritás. | Nem       |
| paraméterek          | Az U-SQL parancsfájlba való átvitelhez használt paraméterek.    | Nem       |
| runtimeVersion      | Az U-SQL motor használandó futásidejű verziója. | Nem       |
| compilationMode     | <p>Az U-SQL fordítási módja. A következő értékek egyikének kell lennie: **Szemantikai:** Csak szemantikai ellenőrzéseket és szükséges józansági ellenőrzéseket hajtson végre, **Teljes:** A teljes fordítás végrehajtása, beleértve a szintaxis-ellenőrzést, optimalizálást, kódgenerálást stb., **SingleBox:** A teljes fordítás végrehajtása a TargetType beállítással a SingleBox értékre. Ha nem ad meg értéket ehhez a tulajdonsághoz, a kiszolgáló határozza meg az optimális fordítási módot. | Nem |

A parancsfájldefiníciót lásd: [SearchLogProcessing.txt.](#sample-u-sql-script) 

## <a name="sample-u-sql-script"></a>Példa U-SQL parancsfájl

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

A fenti parancsfájl példában a bemeneti és kimeneti a parancsfájl határozza meg ** \@a be-és** ** \@kimeneti** paramétereket. Az U-SQL ** \@** parancsfájl ** \@bejövő** és kimenő paramétereinek értékeit a Data Factory dinamikusan továbbítja a "paraméterek" szakasz használatával. 

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
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

Ebben az esetben a bemeneti fájlok továbbra is a /datalake/input mappából kerülnek felvételre, és a kimeneti fájlok a /datalake/output mappában jönnek létre. A fájlnevek dinamikusak az ablak kezdési ideje alapján, amikor a folyamat aktiválódik.  

## <a name="next-steps"></a>További lépések
Az alábbi cikkekben elmagyarázhatja, hogyan alakíthatja át más módon az adatokat: 

* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [Sertésaktivitás](transform-data-using-hadoop-pig.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming tevékenység](transform-data-using-hadoop-streaming.md)
* [Szikraaktivitás](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Gépi tanulási kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
