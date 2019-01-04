---
title: Webes tevékenység az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja webes tevékenység, a Data Factory által támogatott átvitelvezérlési tevékenységek egyikét kell elindítani a folyamatot a REST-végponton.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: shlo
ms.openlocfilehash: adfb30b73bbc9929bbfe3b07bd830d3f278bcc27
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53723688"
---
# <a name="web-activity-in-azure-data-factory"></a>Webes tevékenység az Azure Data Factoryban
A webes tevékenység segítségével meghívható egy egyéni REST-végpont egy Data Factory-folyamatból. Az adatkészleteket és a társított szolgáltatásokat továbbíthatja a tevékenység számára felhasználásra vagy elérés céljára. 

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

## <a name="type-properties"></a>Tulajdonságok

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
név | A webes tevékenység neve | Karakterlánc | Igen
type | Meg kell **WebActivity**. | Karakterlánc | Igen
method | A céloldali végpont metódus REST API-t. | karakterlánc. <br/><br/>A támogatott típusok: "GET", "POST", "PUT" | Igen
url | Céloldali végpont és az elérési út | Karakterlánc (vagy a resultType kifejezés karakterlánc). A tevékenység időtúllépést eredményeznek egy hiba miatt 1 perc, ha nem kapott választ a végpontról. | Igen
A fejlécek | A kérelmet küldött fejlécek. Például állítsa be a nyelvet, és írja be egy kérelemre: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Karakterlánc (vagy a resultType kifejezés karakterlánc) | Igen, a Content-type fejléc szükség. `"headers":{ "Content-Type":"application/json"}`
törzs | A tartalom a végpontnak küldött jelöli.  | Karakterlánc (vagy a resultType kifejezés karakterlánc). <br/><br/>A séma, a kérelem adattartalom [kérelem hasznos séma](#request-payload-schema) szakaszban. | POST és PUT-metódusok szükséges.
hitelesítés | A végpont meghívására szolgáló használt hitelesítési módszert. Támogatott típusok a következők: "Basic vagy ClientCertificate." További információkért lásd: [hitelesítési](#authentication) szakaszban. Ha a hitelesítés nem szükséges, zárja ki ezt a tulajdonságot. | Karakterlánc (vagy a resultType kifejezés karakterlánc) | Nem
adatkészletek | Adatkészletek listája a végpontnak átadott. | Adatkészlet hivatkozások tömbje. Lehet üres tömb. | Igen
linkedServices | Társított szolgáltatások listájában végpontnak átadott. | Társított szolgáltatás hivatkozik tömbje. Lehet üres tömb. | Igen

> [!NOTE]
> REST-végpontokat meghívó a webes tevékenység a JSON típusú választ kell visszaadnia. A tevékenység időtúllépést eredményeznek egy hiba miatt 1 perc, ha nem kapott választ a végpontról.

Az alábbi táblázat bemutatja a JSON-tartalmak követelményei:

| Érték típusa | A kérés törzse | Választörzs |
|---|---|---|
|JSON-objektum | Támogatott | Támogatott |
|JSON-tömb | Támogatott <br/>(Jelenlegi, JSON-tömbök nem működik egy hiba miatt. A javítás folyamatban van.) | Nem támogatott |
| JSON-értéket | Támogatott | Nem támogatott |
| Nem – JSON-típus | Nem támogatott | Nem támogatott |
||||

## <a name="authentication"></a>Hitelesítés

### <a name="none"></a>None
Ha a hitelesítés nem szükséges, nem tartalmazzák a "hitelesítés" tulajdonság.

### <a name="basic"></a>Alapszintű
Adja meg a felhasználónevet és jelszót az alapszintű hitelesítés használata. 

```json
"authentication":{  
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Ügyféltanúsítvány
Adja meg a PFX-fájlok és a jelszó base64-kódolású tartalmát. 

```json
"authentication":{  
   "type":"ClientCertificate",
   "pfx":"****",   
   "password":"****"
}
```

### <a name="managed-identity"></a>Felügyelt identitás

Adja meg az erőforrás URI azonosítója, amelyhez a hozzáférési jogkivonatot fog kérni a data Factory a felügyelt identitások használatával. Az Azure Resource Management API meghívása, használja a `https://management.azure.com/`.

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

## <a name="request-payload-schema"></a>Kérelem hasznos séma
Ha a POST és PUT metódust használja, a szervezet tulajdonság jelöli a a végpontnak küldött hasznos. Társított szolgáltatásokat és adatkészleteket adhat át a hasznos részeként. Íme a sémát az adattartalomhoz: 

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
Ebben a példában a webes tevékenység a folyamat meghívja a REST-végpontot. Egy Azure SQL társított szolgáltatás és az Azure SQL-adatkészlet átadja a végpontot. A REST-végpontot használja az Azure SQL-kapcsolati karakterlánc az Azure SQL-kiszolgálóhoz való csatlakozáshoz, és az SQL Server-példány nevét adja vissza. 

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

### <a name="pipeline-parameter-values"></a>Folyamat paraméterértékek

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Webszolgáltatás végpont kódjához

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
Tekintse meg a többi Data Factory által támogatott átvitelvezérlési tevékenységek: 

- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
