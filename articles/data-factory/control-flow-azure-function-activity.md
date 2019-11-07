---
title: Az Azure Function tevékenység Azure Data Factory
description: Ismerje meg, hogyan futtathat Azure-függvényeket az Azure functions szolgáltatásban egy Data Factory-folyamatban
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: 781c5a579fa0cd0383e95b79df1f81f74008111c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679959"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Az Azure Function tevékenység Azure Data Factory

Az Azure Function tevékenység lehetővé teszi [Azure functions](../azure-functions/functions-overview.md) futtatását egy Data Factory-folyamatban. Azure-függvény futtatásához létre kell hoznia egy társított szolgáltatási kapcsolatát és egy olyan tevékenységet, amely meghatározza a végrehajtandó Azure-függvényt.

A szolgáltatás nyolc perces bevezetéséhez és bemutatásához tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure Function társított szolgáltatás

Az Azure-függvény visszatérési típusának érvényes `JObject`nak kell lennie. (Ne feledje, hogy [](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) a JArray *nem* `JObject`.) A `JObject`tól eltérő visszatérési típusok sikertelenek, és a felhasználói hibákra *adott tartalom nem érvényes JObject*.

| **Tulajdonság** | **Leírás** | **Szükséges** |
| --- | --- | --- |
| type   | A Type tulajdonságot a következőre kell beállítani: **AzureFunction** | igen |
| function alkalmazás URL-címe | Az Azure-függvényalkalmazás URL-címe. A formátum `https://<accountname>.azurewebsites.net`. Ez az URL-cím az **URL-** cím szakaszban található, amikor a függvényalkalmazás megtekinti a Azure Portal  | igen |
| függvény kulcsa | Az Azure-függvény elérési kulcsa. Kattintson a **Manage (kezelés** ) szakaszra a megfelelő függvényhez, és másolja a **függvény** vagy a **gazda kulcsot**. További információ: [Azure FUNCTIONS http-eseményindítók és-kötések](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | igen |
|   |   |   |

## <a name="azure-function-activity"></a>Azure Function tevékenység

| **Tulajdonság**  | **Leírás** | **Megengedett értékek** | **Szükséges** |
| --- | --- | --- | --- |
| név  | A folyamatban szereplő tevékenység neve  | Sztring | igen |
| type  | A tevékenység típusa "AzureFunctionActivity". | Sztring | igen |
| Társított szolgáltatás | Az Azure Function társított szolgáltatás a megfelelő Azure-függvényalkalmazás  | Társított szolgáltatás leírása | igen |
| Függvény neve  | Azon függvény neve az Azure függvényalkalmazásban, amelyre ez a tevékenység hív | Sztring | igen |
| method  | A függvény hívásának REST API metódusa | Támogatott karakterlánc-típusok: "GET", "POST", "PUT"   | igen |
| header  | A kérelembe küldendő fejlécek. Például a nyelv és a típus megadásához a következőre: "headers": {"Accept-Language": "en-us", "Content-Type": "Application/JSON"} | Karakterlánc (vagy resultType karakterláncot tartalmazó kifejezés) | Nem |
| törzse  | a függvény API-metódusának kérésével együtt elküldett törzs  | Sztring (vagy kifejezés resultType) vagy objektummal.   | PUT/POST metódusokhoz szükséges |
|   |   |   | |

Tekintse meg a kérelem hasznos adatainak sémáját a [kérelem hasznos adatainak sémája](control-flow-web-activity.md#request-payload-schema) szakaszban.

## <a name="routing-and-queries"></a>Útválasztás és lekérdezések

Az Azure Function tevékenység támogatja az **útválasztást**. Ha például az Azure-függvény végpontja `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`, akkor az Azure Function tevékenységben használni kívánt `functionName` `<functionName>/<value>`. Ezt a függvényt parametrizálja a kívánt `functionName` futtatásához.

Az Azure Function tevékenység is támogatja a **lekérdezéseket**. A lekérdezésnek a `functionName`részeként kell szerepelnie. Ha például a függvény neve `HttpTriggerCSharp`, és a felvenni kívánt lekérdezés `name=hello`, akkor a `functionName` az Azure Function tevékenységben `HttpTriggerCSharp?name=hello`ként hozható létre. Ez a függvény konfigurálható úgy, hogy az érték a futásidőben is meghatározható legyen.

## <a name="timeout-and-long-running-functions"></a>Időtúllépés és hosszan futó függvények

A beállításokban konfigurált `functionTimeout`-beállítástól függetlenül Azure Functions időtúllépés a 230 másodperc után. További információkért tekintse meg [ezt a cikket](../azure-functions/functions-versions.md#timeout). A viselkedés megkerüléséhez hajtson végre egy aszinkron mintát, vagy használja a Durable Functions. A Durable Functions előnye, hogy saját állapot-követési mechanizmust biztosítanak, így nem kell saját megvalósítást végrehajtania.

További információ a Durable Functionsről [ebben a cikkben](../azure-functions/durable/durable-functions-overview.md). Beállíthat egy Azure functions-tevékenységet a tartós függvény meghívásához, amely egy másik URI-val (például [a példával](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery)) kapcsolatos választ ad vissza. Mivel `statusQueryGetUri` a 202 HTTP-állapotot adja vissza, miközben a függvény fut, a függvény állapotát egy webes tevékenység használatával kérdezheti le. Egyszerűen állítson be egy webes tevékenységet a `url` mezővel, amely `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`ra van beállítva. Ha a tartós funkció befejeződik, a függvény kimenete a webes tevékenység kimenete lesz.


## <a name="sample"></a>Sample

Megtalálhatja az Azure-függvényt használó Data Factory mintáját, amellyel kinyerheti a Tar [-fájl tartalmát](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>További lépések

További információ a Data Factory kapcsolódó tevékenységekről [Azure Data Factory folyamatokban és tevékenységekben](concepts-pipelines-activities.md).
