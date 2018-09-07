---
title: Adatok átalakítása a használatával a U-SQL-szkript – Azure |} A Microsoft Docs
description: Ismerje meg, feldolgozása és átalakítási adatok Azure Data Lake Analytics számítási szolgáltatás az U-SQL-parancsfájlok futtatásával.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: abnarain
ms.openlocfilehash: cbe4d3931a5e7b279218a1f56a3842efbc238780
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44053567"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Adatok átalakítása az Azure Data Lake Analytics U-SQL-parancsfájlok futtatásával 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-usql-activity.md)
> * [Aktuális verzió](transform-data-using-data-lake-analytics.md)

Az Azure data factory-folyamatot az adatok az társított storage-szolgáltatásokban csatolt számítási szolgáltatások használatával dolgozza fel. A tevékenységek, ahol minden egyes tevékenységhez meghatározott feldolgozási műveletet hajt végre egy feladatütemezési tartalmazza. Ez a cikk ismerteti a **Data Lake Analytics U-SQL-tevékenység** futtat egy **U-SQL** a parancsfájl egy **Azure Data Lake Analytics** számítási társított szolgáltatás. 

Hozzon létre egy Azure Data Lake Analytics-fiókot egy Data Lake Analytics U-SQL-tevékenység a folyamat létrehozása előtt. Az Azure Data Lake Analytics kapcsolatos további információkért lásd: [Ismerkedés az Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Az Azure Data Lake Analytics hivatkozott szolgáltatást
Létrehoz egy **Azure Data Lake Analytics** társított szolgáltatást, az Azure Data Lake Analytics számítási szolgáltatás az Azure data factoryt. A Data Lake Analytics U-SQL-tevékenység, a folyamat a társított szolgáltatásra vonatkozik. 

Az alábbi táblázat ismerteti a JSON-definíciójában használt általános tulajdonságokat. 

| Tulajdonság                 | Leírás                              | Szükséges                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **type**                 | A type tulajdonságot kell megadni: **AzureDataLakeAnalytics**. | Igen                                      |
| **Fióknév**          | Az Azure Data Lake Analytics-fiók neve.  | Igen                                      |
| **dataLakeAnalyticsUri** | Az Azure Data Lake Analytics URI.           | Nem                                       |
| **subscriptionId**       | Azure-előfizetés azonosítója                    | Nem                                       |
| **resourceGroupName**    | Azure-erőforráscsoport neve                | Nem                                       |

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése
Az Azure Data Lake Analytics hivatkozott szolgáltatást egy egyszerű szolgáltatásnév hitelesítése az Azure Data Lake Analytics szolgáltatáshoz való csatlakozáshoz szükséges. Egyszerű szolgáltatásnév hitelesítése alkalmazás entitás regisztrálása az Azure Active Directory (Azure AD), és hozzáférést adni azt a Data Lake Analytics és a Data Lake Store használ. Részletes lépéseiért lásd: [szolgáltatások közötti hitelesítés](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Jegyezze fel a következő értékeket, mert a társított szolgáltatás definiálásához használja:

* Alkalmazásazonosító
* Alkalmazáskulcs 
* Bérlőazonosító

Az Azure Data Lake Anatlyics használatával a szolgáltatás egyszerű engedély megadása a [felhasználó hozzáadása varázsló](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user).

Egyszerű szolgáltatásnév hitelesítése használja a következő tulajdonságok megadásával:

| Tulajdonság                | Leírás                              | Szükséges |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Adja meg az alkalmazás ügyfél-azonosítót.     | Igen      |
| **servicePrincipalKey** | Adja meg az alkalmazáskulcsot.           | Igen      |
| **bérlő**              | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Kérheti a viszi az egérmutatót az Azure portal jobb felső sarkában. | Igen      |

**Példa: Egyszerű szolgáltatásnév hitelesítése**
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

A társított szolgáltatás kapcsolatos további információkért lásd: [társított szolgáltatások számítása](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-tevékenység
A következő JSON-kódrészlet definiál egy Data Lake Analytics U-SQL-tevékenység rendelkező folyamatot. A tevékenység meghatározásában tartalmaz egy hivatkozást a korábban létrehozott Azure Data Lake Analytics hivatkozott szolgáltatást. Egy Data Lake Analytics U-SQL parancsfájl végrehajtása a Data Factory elküldi a parancsfájl a megadott, a Data Lake Analytics és a Data Lake Analytics beolvasni és a kimeneti parancsfájlt a szükséges bemeneteit és kimeneteit van definiálva. 

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

A következő táblázat ismerteti a neveket és leírásokat erre a tevékenységre jellemző tulajdonságok. 

| Tulajdonság            | Leírás                              | Szükséges |
| :------------------ | :--------------------------------------- | :------- |
| név                | A folyamat a tevékenység neve     | Igen      |
| leírás         | A tevékenység leírása leíró szöveg.  | Nem       |
| type                | Data Lake Analytics U-SQL-tevékenység, a tevékenység típusa van **DataLakeAnalyticsU-SQL**. | Igen      |
| linkedServiceName   | Az Azure Data Lake Analytics hivatkozott szolgáltatást. Ezt a társított szolgáltatást kapcsolatos további információkért lásd: [társított szolgáltatások számítása](compute-linked-services.md) cikk.  |Igen       |
| scriptPath          | A U-SQL parancsfájlt tartalmazó mappa elérési útja. A fájl neve, a kis-és nagybetűket. | Igen      |
| scriptLinkedService | Társított szolgáltatás, amely összekapcsolja a **Azure Data Lake Store** vagy **Azure Storage** , amely tartalmazza a parancsfájl a data factoryhoz | Igen      |
| degreeOfParallelism | A feladat futtatásához egyidejűleg használt csomópontok maximális száma. | Nem       |
| prioritás            | Meghatározza, hogy mely feladatok közül, hogy a rendszer várólistára helyezi, melyet futtatni kíván van kiválasztva. Az alacsonyabb a szám, annál magasabb a prioritás. | Nem       |
| paraméterek          | A U-SQL parancsfájl átadandó paramétereket.    | Nem       |
| runtimeVersion      | Futtatókörnyezet-verzióját a U-SQL-motort használja. | Nem       |
| compilationMode     | <p>Fordítási mód az U-SQL. Ezek az értékek egyikének kell lennie: **Szemantikus:** csak hajtsa végre a szükséges megerősítések, és a szemantikai ellenőrzési **teljes:** hajtsa végre a teljes fordítás, beleértve a szintaxis-ellenőrzés, optimalizálás, generování kódu, stb., **SingleBox:** hajtsa végre a teljes fordítás SingleBox TargetType beállítással együtt. Ez a tulajdonság értékét nem adja meg, ha a kiszolgáló meghatározza, hogy az optimális fordítási mód. | Nem |

A Data Factory elküldi a lásd [SearchLogProcessing.txt szkriptet definíciójának](#sample-u-sql-script) a parancsfájl-definíciójában. 

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

A fenti szkript a példában a bemeneti és kimeneti parancsfájl van definiálva **@in** és **@out** paramétereket. A tartozó értékeket **@in** és **@out** a U-SQL parancsfájl átadott paraméterek dinamikusan adat-előállítók által "parameters" szakaszában. 

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
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

Ebben az esetben a bemeneti fájlok továbbra is mértékének /datalake/input mappából, és a kimeneti fájlok jönnek létre a /datalake/output mappában. A fájlok nevei megkülönböztetik a dinamikus alapján a folyamat elindításakor lekéri az átadott ablak indítási időpontja.  

## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikkek, amelyek bemutatják, hogyan alakíthat át adatokat, egyéb módon: 

* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce-tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streamelési tevékenységben](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
