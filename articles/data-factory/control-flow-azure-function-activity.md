---
title: Azure-függvénytevékenység az Azure Data Factoryban
description: Ismerje meg, hogyan használhatja az Azure-függvény tevékenységet egy Azure-függvény adatfeldolgozó folyamatban való futtatásához
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
ms.openlocfilehash: ee2e59e794cf34a8fd5043a56867a81c2537f1ae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415312"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure-függvénytevékenység az Azure Data Factoryban
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Az Azure-függvény tevékenység lehetővé teszi az [Azure Functions](../azure-functions/functions-overview.md) futtatását egy Data Factory-folyamatban. Az Azure-függvény futtatásához létre kell hoznia egy összekapcsolt szolgáltatáskapcsolatot, és egy tevékenységet, amely meghatározza az Azure-függvényt, amelyet végrehajtani kíván.

A funkció nyolcperces bemutatásához és bemutatásához tekintse meg az alábbi videót:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure-függvényhez csatolt szolgáltatás

Az Azure függvény visszatérési típusának `JObject`érvényesnek kell lennie. (Ne feledje, hogy a `JObject` [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *nem* .) A sikertelenektől `JObject` eltérő visszatérési típus nem felel meg, és a felhasználói hiba *választartalma nem érvényes JObject*objektum.

| **Tulajdonság** | **Leírás** | **Szükséges** |
| --- | --- | --- |
| type   | A típustulajdonságot a következőre kell állítani: **AzureFunction** | igen |
| függvény alkalmazás url-címe | Az Azure Függvényalkalmazás URL-címe. A `https://<accountname>.azurewebsites.net`formátum a . Ez az URL-cím az **URL-cím** alatt található érték, amikor a Függvényalkalmazást az Azure Portalon tekinti meg.  | igen |
| funkcióbillentyű | Hozzáférési kulcs az Azure-függvényhez. Kattintson a megfelelő funkció **Manage** szakaszára, és másolja a **Függvénykulcsot** vagy a **Gazdakulcsot**. További információ: [Az Azure Functions HTTP-eseményindítók és kötések](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | igen |
|   |   |   |

## <a name="azure-function-activity"></a>Azure-függvény tevékenysége

| **Tulajdonság**  | **Leírás** | **Megengedett értékek** | **Szükséges** |
| --- | --- | --- | --- |
| név  | A folyamatban lévő tevékenység neve  | Sztring | igen |
| type  | A tevékenység típusa "AzureFunctionActivity" | Sztring | igen |
| összekapcsolt szolgáltatás | Az Azure-függvényhez kapcsolódó szolgáltatás a megfelelő Azure Függvényalkalmazáshoz  | Csatolt szolgáltatás hivatkozása | igen |
| függvény neve  | A tevékenység által meghívott Azure Függvényalkalmazás függvényének neve | Sztring | igen |
| method  | REST API-metódus a függvényhíváshoz | Karakterlánc támogatott típusai: "GET", "POST", "PUT"   | igen |
| header  | A kérésnek küldött fejlécek. Például a nyelv és a beírás beállítása egy kérelemre: "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | Karakterlánc (vagy kifejezés resultKarakterlánctal) | Nem |
| body (Törzs)  | a függvény api metódusához küldött kéréssel együtt küldött  | Karakterlánc (vagy kifejezés resultType karakterláncot) vagy objektumot.   | Put/POST metódusok esetén szükséges |
|   |   |   | |

Tekintse meg a kérelem hasznos adatának sémáját a [Hasznos séma](control-flow-web-activity.md#request-payload-schema) kérése szakaszban.

## <a name="routing-and-queries"></a>Útválasztás és lekérdezések

Az Azure-függvénytevékenység támogatja az **útválasztást.** Ha például az Azure-függvény `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`rendelkezik `functionName` a végponttal, akkor `<functionName>/<value>`az Azure-függvénytevékenységben való használat a. Ezt a függvényt paraméterezheti `functionName` a kívánt futásidőben való megadásához.

Az Azure-függvénytevékenység a **lekérdezéseket**is támogatja. A lekérdezést a részét a `functionName`részét kell tartalmaznia. Ha például a függvény `HttpTriggerCSharp` neve, és a lekérdezés, amelyet fel szeretne venni, a `name=hello`, majd létrehozhatja az `functionName` Azure-függvénytevékenységben. `HttpTriggerCSharp?name=hello` Ez a függvény paraméterezhető, így az érték futásidőben határozható meg.

## <a name="timeout-and-long-running-functions"></a>Idő- és hosszú futási függvények

Az Azure Functions 230 másodperc `functionTimeout` után időzít, függetlenül a beállításokban beállított beállítástól. További információt [ebben a cikkben](../azure-functions/functions-versions.md#timeout)talál. A probléma kerülő megoldásához kövesse az aszinkron mintát, vagy használja a Tartós függvények funkciót. A tartós funkciók előnye, hogy saját állapotkövetési mechanizmust kínálnak, így nem kell végrehajtania a sajátját.

A tartós funkciókról ebben a cikkben olvashat [bővebben.](../azure-functions/durable/durable-functions-overview.md) Beállíthat egy Azure-függvénytevékenységet a tartós függvény hívására, amely egy másik URI-val válaszol, például [ebben a példában.](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery) Mivel `statusQueryGetUri` a függvény futása közben a HTTP 202-es állapotot adja vissza, a függvény állapotát webes tevékenység használatával lelehet hívni. Egyszerűen állítson be egy `url` webes `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`tevékenységet úgy, hogy a mező je: . A tartós függvény befejezésekor a függvény kimenete a webes tevékenység kimenete lesz.


## <a name="sample"></a>Sample

Itt talál egy adatgyárból mintát, amely egy Azure-függvényt használ a kátrányos fájl tartalmának [kinyeréséhez.](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)

## <a name="next-steps"></a>További lépések

További információ a folyamatok adatfeldolgozói tevékenységeiről [és az Azure Data Factory tevékenységeiről.](concepts-pipelines-activities.md)
