---
title: Az Azure Data Factory tevékenység webes |} Microsoft Docs
description: Ismerje meg, hogyan használható fel a webes tevékenység, a Data Factory által támogatott vezérlésfolyam-tevékenységek egyik egy láncból REST-végpont meghívása.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: shlo
ms.openlocfilehash: 58faed48f5031b26f1340f3766fdd8bdc6bd2ccb
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="web-activity-in-azure-data-factory"></a>Az Azure Data Factory webes tevékenység
A webes tevékenység segítségével meghívható egy egyéni REST-végpont egy Data Factory-folyamatból. Az adatkészleteket és a társított szolgáltatásokat továbbíthatja a tevékenység számára felhasználásra vagy elérés céljára. 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd [a Data Factory 1. verziójának dokumentációját](v1/data-factory-introduction.md).

## <a name="syntax"></a>Szintaxis

```json
{  
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{  
      "method":"Post",
      "url":"<URLEndpoint>",
      "headers":{  
         "Content-Type":"application/json"
      },
      "authentication":{  
         "type":"ClientCertificate",  
         "pfx":"****",
         "password":"****"
      },
      "datasets":[  
         {  
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{  
               ...
            }
         }
      ],
      "linkedServices":[  
         {  
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>A típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
név | A webes tevékenység neve. | Karakterlánc | Igen
type | Meg kell **WebActivity**. | Karakterlánc | Igen
method | REST API-metódusra a cél-végponthoz. | Karakterlánc. <br/><br/>A támogatott típusok: "GET", "POST", "PUT" | Igen
url | Cél-végponthoz és elérési útja | Karakterlánc (vagy a resultType kifejezés karakterlánc). A tevékenység időtúllépési lesz a hibával 1 perc, ha nem kapott választ a végpontról. | Igen
fejlécek | A kérelemben küldött fejléceket. Ahhoz például, hogy állítsa be a nyelvét és típusát kérelem: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Karakterlánc (vagy a resultType kifejezés karakterlánc) | Igen, a Content-type fejléc szükség. `"headers":{ "Content-Type":"application/json"}`
törzs | A tartalom a végpontnak küldött jelöli. A PUT/POST metódusok szükséges.  | Karakterlánc (vagy a resultType kifejezés karakterlánc). <br/><br/>Tekintse meg a séma, a kérelem hasznos [kérelem hasznos séma](#request-payload-schema) szakasz. | Nem
hitelesítés | A végpont meghívása használt hitelesítési módszert. Támogatott típusok a következők: "Basic vagy ClientCertificate." További információkért lásd: [hitelesítési](#authentication) szakasz. Ha hitelesítési nincs szükség, zárja ki ezt a tulajdonságot. | Karakterlánc (vagy a resultType kifejezés karakterlánc) | Nem
Adatkészletek | A végpont átadott adatkészletek listáját. | A tömb adatkészlet hivatkozik. Üres tömb lehet. | Igen
linkedServices | Végpont átadott összekapcsolt szolgáltatások listája. | A kapcsolódószolgáltatás-hivatkozások tömbje. Üres tömb lehet. | Igen

> [!NOTE]
> A webes tevékenység elindítja a REST-végpontok egy válasz JSON típusú kell visszaadnia. A tevékenység időtúllépési lesz a hibával 1 perc, ha nem kapott választ a végpontról.

Az alábbi táblázat a JSON-tartalmak követelményei:

| Érték típusa | Kérelem törzse | Választörzs |
|---|---|---|
|JSON-objektum | Támogatott | Támogatott |
|JSON-tömb | Támogatott <br/>(Jelenleg JSON-tömbök nem működnek a hiba miatt. Javítás folyamatban van.) | Nem támogatott |
| JSON-érték | Támogatott | Nem támogatott |
| Nem-JSON típusa | Nem támogatott | Nem támogatott |
||||

## <a name="authentication"></a>Hitelesítés

### <a name="none"></a>None
Ha a hitelesítés nem szükséges, nem tartalmaznak a "hitelesítés" tulajdonság.

### <a name="basic"></a>Alapszintű
Adja meg a felhasználónevet és jelszót ehhez az egyszerű hitelesítéssel. 

```json
"authentication":{  
   "type":"Basic,
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Ügyféltanúsítvány
Adja meg a base64-kódolású tartalmát egy PFX-fájlt, és a jelszót. 

```json
"authentication":{  
   "type":"ClientCertificate",
   "pfx":"****",   
   "password":"****"
}
```
## <a name="request-payload-schema"></a>Kérelem hasznos séma
Ha a FELADÁS egy vagy több/PUT metódust használja, a body tulajdonság jelöli a tartalom a végpontnak küldött. A tartalom részeként átadhatók társított szolgáltatások és adatkészletek. A séma, a tartalom itt található: 

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
} 
```

## <a name="example"></a>Példa
Ebben a példában a webes tevékenység az adatcsatorna meghívja a többi végpont. A végpont Azure SQL társított szolgáltatásnak, és az Azure SQL adatkészletet továbbítja. A többi végpont az Azure SQL-kapcsolati karakterlánc az Azure SQL-kiszolgáló elérésére használt, és az SQL server-példány nevét adja vissza. 

### <a name="pipeline-definition"></a>Folyamat meghatározása

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>Feldolgozási sor paraméterértékek

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Webes szolgáltatási végpont kódja

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>További lépések
Tekintse meg a többi adat-előállító által támogatott vezérlésfolyam-tevékenységek: 

- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
