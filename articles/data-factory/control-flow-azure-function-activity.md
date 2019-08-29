---
title: Az Azure Function tevékenység a Azure Data Factoryban | Microsoft Docs
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
ms.openlocfilehash: 292fe858b85faef69b9df2dbdf54e7061ed56fa2
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142509"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Az Azure Function tevékenység Azure Data Factory

Az Azure Function tevékenység lehetővé teszi [Azure functions](../azure-functions/functions-overview.md) futtatását egy Data Factory-folyamatban. Azure-függvény futtatásához létre kell hoznia egy társított szolgáltatási kapcsolatát és egy olyan tevékenységet, amely meghatározza a végrehajtandó Azure-függvényt.

A szolgáltatás nyolc perces bevezetéséhez és bemutatásához tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure Function társított szolgáltatás

Az Azure-függvény visszatérési típusának érvényesnek `JObject`kell lennie. (Ne feledje, hogy [](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) a JArray `JObject`nem.) Bármely visszatérési típus `JObject` , amely nem sikerül, és a felhasználói hiba miatti *válasz tartalma nem érvényes JObject*.

| **Tulajdonság** | **Leírás** | **Kötelező** |
| --- | --- | --- |
| type   | A Type tulajdonságot a következőre kell beállítani: **AzureFunction** | igen |
| function alkalmazás URL-címe | Az Azure-függvényalkalmazás URL-címe. Formátum: `https://<accountname>.azurewebsites.net`. Ez az URL-cím az **URL-** cím szakaszban található, amikor a függvényalkalmazás megtekinti a Azure Portal  | igen |
| függvény kulcsa | Az Azure-függvény elérési kulcsa. Kattintson a **Manage (kezelés** ) szakaszra a megfelelő függvényhez, és másolja a **függvény** vagy a **gazda kulcsot**. További információ: [HTTP-eseményindítók és-kötések Azure Functions](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | igen |
|   |   |   |

## <a name="azure-function-activity"></a>Azure Function tevékenység

| **Tulajdonság**  | **Leírás** | **Megengedett értékek** | **Kötelező** |
| --- | --- | --- | --- |
| name  | A folyamatban szereplő tevékenység neve  | Sztring | igen |
| type  | A tevékenység típusa "AzureFunctionActivity". | Sztring | igen |
| társított szolgáltatás | Az Azure Function társított szolgáltatás a megfelelő Azure-függvényalkalmazás  | Társított szolgáltatás leírása | igen |
| függvény neve  | Azon függvény neve az Azure függvényalkalmazásban, amelyre ez a tevékenység hív | Sztring | igen |
| metódus  | A függvény hívásának REST API metódusa | Támogatott karakterlánc-típusok: "GET", "POST", "PUT"   | igen |
| header  | A kérelembe küldendő fejlécek. Például a nyelv és a típus megadásához a következőre: "headers": {"Accept-Language": "en-us", "Content-Type": "Application/JSON"} | Karakterlánc (vagy resultType karakterláncot tartalmazó kifejezés) | Nem |
| törzs  | a függvény API-metódusának kérésével együtt elküldett törzs  | Sztring (vagy kifejezés resultType) vagy objektummal.   | PUT/POST metódusokhoz szükséges |
|   |   |   | |

Tekintse meg a kérelem hasznos adatainak sémáját a [kérelmek hasznos](control-flow-web-activity.md#request-payload-schema) adatait tartalmazó sémában.

## <a name="routing-and-queries"></a>Útválasztás és lekérdezések

Az Azure Function tevékenység támogatjaaz útválasztást. Ha például az Azure-függvény rendelkezik végponttal `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`, `functionName` akkor az Azure Function tevékenységben `<functionName>/<value>`való használathoz. Ezt a függvényt parametrizálja, hogy a kívánt `functionName` futtatókörnyezet elérhető legyen.

Az Azure Function tevékenység is támogatjaa lekérdezéseket. A lekérdezésnek a `functionName`részeként szerepelnie kell. Ha például `HttpTriggerCSharp` a függvény neve és a `name=hello`felvenni kívánt lekérdezés, `functionName` akkor az az Azure Function tevékenységben `HttpTriggerCSharp?name=hello`a következőként hozható létre:. Ez a függvény konfigurálható úgy, hogy az érték a futásidőben is meghatározható legyen.

## <a name="timeout-and-long-running-functions"></a>Időtúllépés és hosszan futó függvények

A beállításokban konfigurált beállítástól függetlenül Azure functions időtúllépés a `functionTimeout` 230 másodperc után. További információkért tekintse meg [ezt a cikket](../azure-functions/functions-versions.md#timeout). A viselkedés megkerüléséhez hajtson végre egy aszinkron mintát, vagy használja a Durable Functions. A Durable Functions előnye, hogy saját állapot-követési mechanizmust biztosítanak, így nem kell saját megvalósítást végrehajtania.

További információ a Durable Functionsről [ebben a cikkben](../azure-functions/durable/durable-functions-overview.md). Beállíthat egy Azure functions-tevékenységet a tartós függvény meghívásához, amely egy másik URI-val (például [a példával](../azure-functions/durable/durable-functions-http-api.md#http-api-url-discovery)) kapcsolatos választ ad vissza. Mivel `statusQueryGetUri` a a 202 http-állapotot adja vissza, miközben a függvény fut, a függvény állapotát egy webes tevékenység használatával kérdezheti le. Egyszerűen állítson be egy webes tevékenységet, `url` amelynek a mezője a `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`értékre van állítva. Ha a tartós funkció befejeződik, a függvény kimenete a webes tevékenység kimenete lesz.


## <a name="sample"></a>Minta

Megtalálhatja az Azure-függvényt használó Data Factory mintáját, amellyel kinyerheti a tar-fájl [](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)tartalmát.

## <a name="next-steps"></a>További lépések

További információ a Data Factory kapcsolódó tevékenységekről [Azure Data Factory folyamatokban és tevékenységekben](concepts-pipelines-activities.md).
