---
title: Webes tevékenység az Azure Data Factoryban
description: Megtudhatja, hogy a Data Factory által támogatott webtevékenység segítségével hogyan hívhat meg egy REST-végpontot egy folyamatból.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: a5cdb24a80dcbd95e4ccc59dd55f4acb9ae18060
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417895"
---
# <a name="web-activity-in-azure-data-factory"></a>Webes tevékenység az Azure Data Factoryban
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


A webes tevékenység segítségével meghívható egy egyéni REST-végpont egy Data Factory-folyamatból. Az adatkészleteket és a társított szolgáltatásokat továbbíthatja a tevékenység számára felhasználásra vagy elérés céljára.

> [!NOTE]
> A webes tevékenység csak nyilvánosan elérhető URL-címeket hívhat meg. Nem támogatott a magán virtuális hálózatban üzemeltetett URL-címek.

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

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
név | A webes tevékenység neve | Sztring | Igen
type | A **WebActivity (WebActivity**) beállításra kell tini. | Sztring | Igen
method | Rest API-módszer a célvégponthoz. | Sztring. <br/><br/>Támogatott típusok: "GET", "POST", "PUT" | Igen
url | Célvégpont és elérési út | Karakterlánc (vagy kifejezés resultType of string). A tevékenység időtúllépések 1 perc egy hiba, ha nem kap választ a végponttól. | Igen
Fejlécek | A kérésnek küldött fejlécek. Például a nyelv és a beírás `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`beállítása a kérelemben: . | Karakterlánc (vagy kifejezés resultKarakterlánctal) | Igen, tartalomtípusú fejléc szükséges. `"headers":{ "Content-Type":"application/json"}`
body (Törzs) | A végpontra küldött hasznos adat.  | Karakterlánc (vagy kifejezés resultType of string). <br/><br/>Tekintse meg a kérelem hasznos adatának sémáját a [Hasznos séma kérése](#request-payload-schema) szakaszban. | A POST/PUT módszerekhez szükséges.
hitelesítés | A végpont hívására használt hitelesítési módszer. A támogatott típusok az "Alapszintűek vagy ügyféltanúsítvány". További információt a [Hitelesítés](#authentication) szakaszban talál. Ha nincs szükséges hitelesítés, zárja ki ezt a tulajdonságot. | Karakterlánc (vagy kifejezés resultKarakterlánctal) | Nem
adathalmazok | A végpontnak átadott adatkészletek listája. | Adatkészlet-hivatkozások tömbje. Lehet egy üres tömb. | Igen
csatolt szolgáltatások | A végpontnak átadott csatolt szolgáltatások listája. | Csatolt szolgáltatáshivatkozások tömbje. Lehet egy üres tömb. | Igen

> [!NOTE]
> REST-végpontok, amelyek a webes tevékenység meghívja kell visszaadnia a válasz típusú JSON. A tevékenység időtúllépések 1 perc egy hiba, ha nem kap választ a végponttól.

Az alábbi táblázat a JSON-tartalomra vonatkozó követelményeket mutatja be:

| Érték típusa | A kérés törzse | Választörzs |
|---|---|---|
|JSON-objektum | Támogatott | Támogatott |
|JSON-tömb | Támogatott <br/>(Jelenleg a JSON-tömbök nem működnek hiba miatt. A javítás folyamatban van.) | Nem támogatott |
| JSON-érték | Támogatott | Nem támogatott |
| Nem JSON-típus | Nem támogatott | Nem támogatott |
||||

## <a name="authentication"></a>Hitelesítés

Az alábbiakban a webes tevékenység támogatott hitelesítési típusai láthatók.

### <a name="none"></a>None

Ha nincs szükséges hitelesítés, ne adja meg a "hitelesítés" tulajdonságot.

### <a name="basic"></a>Basic

Adja meg az alapfokú hitelesítéshez használandó felhasználónevet és jelszót.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Ügyféltanúsítvány

Adja meg a PFX-fájl base64 kódolású tartalmát és a jelszót.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Felügyelt identitás

Adja meg azt az erőforrás-uri-t, amelyhez a hozzáférési jogkivonatot a rendszer az adat-előállító felügyelt identitásának használatával kéri. Az Azure Resource Management API `https://management.azure.com/`hívásához használja a használatát. A felügyelt identitások működéséről az [Azure-erőforrások felügyelt identitások áttekintése lapon olvashat bővebben.](/azure/active-directory/managed-identities-azure-resources/overview)

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Ha az adatgyár git-tárházzal van konfigurálva, az alapszintű vagy ügyféltanúsítvány-hitelesítés használatához az Azure Key Vaultban kell tárolnia a hitelesítő adatait. Az Azure Data Factory nem tárolja a jelszavakat a gitben.

## <a name="request-payload-schema"></a>Hasznos adatséma kérése
A POST/PUT metódus használatakor a törzs tulajdonság a végpontnak küldött hasznos adat. A tartalom részeként átadhatja a csatolt szolgáltatásokat és adatkészleteket. Itt van a hasznos adat sémája:

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
Ebben a példában a folyamat webes tevékenysége rest-végpontot hív meg. Egy Azure SQL-kapcsolattal összekapcsolt szolgáltatást és egy Azure SQL-adatkészletet avégpontra továbbítja. A REST végpont az Azure SQL-kapcsolati karakterlánc ot használja az Azure SQL-kiszolgálóhoz való csatlakozáshoz, és visszaadja az SQL-kiszolgáló példányának nevét.

### <a name="pipeline-definition"></a>Csővezeték definíciója

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

### <a name="pipeline-parameter-values"></a>Csővezeték paraméterértékei

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Webszolgáltatás végpontkódja

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
Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamattevékenységeket:

- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
