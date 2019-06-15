---
title: Azure-függvény tevékenység az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure-függvény tevékenység egy Azure-függvényt a Data Factory-folyamatok futtatásához
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 82786b8f01ce409179f4ddd37127679f9357cd0e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64727051"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure-függvény tevékenység az Azure Data Factoryban

Az Azure-függvény tevékenység futtatását teszi [Azure Functions](../azure-functions/functions-overview.md) a Data Factory-folyamatok. Szeretne futtatni egy Azure-függvényt, szeretne létrehozni egy társított szolgáltatás kapcsolatot és a egy tevékenységgel, amely meghatározza az Azure-függvény, amely azt tervezi, hogy hajtsa végre.

Egy nyolc perces bevezető és a funkció bemutatójáért tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure-függvény társított szolgáltatás

Az Azure-függvény visszatérési típusával van, úgy, hogy érvényes `JObject`. (Vegye figyelembe, hogy [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) van *nem* egy `JObject`.) Más, a visszatérési típus `JObject` sikertelen lesz, és a felhasználói hiba lép fel a *válasz tartalma nem egy érvényes JObject*.

| **Tulajdonság** | **Leírás** | **Kötelező** |
| --- | --- | --- |
| type   | A type tulajdonságot kell beállítani: **AzureFunction** | igen |
| függvény URL-címe | Az Azure-Függvényalkalmazás URL-címe. A formátum `https://<accountname>.azurewebsites.net`. Az URL-cím értéke alapján **URL-cím** az Azure Portalon a Függvényalkalmazás megtekintésekor szakasz  | igen |
| billentyűt | Az Azure-függvény hozzáférési kulcsát. Kattintson a a **kezelés** a megfelelő függvény szakaszt, és másolja vagy a **Függvénykulcs** vagy a **állomáskulcs**. Ismerje meg, itt: [Az Azure Functions – HTTP-eseményindítók és kötések](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | igen |
|   |   |   |

## <a name="azure-function-activity"></a>Azure-függvény tevékenység

| **Tulajdonság**  | **Leírás** | **Megengedett értékek** | **Kötelező** |
| --- | --- | --- | --- |
| name  | A folyamat a tevékenység neve  | String | igen |
| type  | A tevékenység típus "AzureFunctionActivity" | String | igen |
| Társított szolgáltatás | A társított Azure-függvény szolgáltatás a megfelelő Azure-függvényalkalmazás  | Társított szolgáltatás hivatkozása | igen |
| Függvény neve  | Az Azure-Függvényalkalmazást, amely meghívja ezt a tevékenységet a függvény neve | String | igen |
| method  | A függvény hívásához szükséges REST API-metódus | Karakterlánc típusok támogatottak: "GET", "POST", "PUT"   | igen |
| header  | A kérelmet küldött fejlécek. Például állítsa be a nyelvet, és írja be egy kérelemre: "fejlécek": {"Accept-nyelv": "en-us", "Content-Type": "application/json"} | Karakterlánc (vagy a resultType kifejezés karakterlánc) | Nem |
| Törzs  | a függvény api-metódus a kéréssel együtt küldött törzs  | Karakterlánc (vagy a karakterlánc a resultType kifejezés) vagy az objektum.   | PUT/POST metódusok szükséges |
|   |   |   | |

A séma, a kérelem adattartalom [kérelem hasznos séma](control-flow-web-activity.md#request-payload-schema) szakaszban.

## <a name="routing-and-queries"></a>Az Útválasztás és lekérdezések

Az Azure-függvény tevékenység támogatja **útválasztási**. Például, ha az Azure-függvény a végponttal rendelkezik `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`, akkor a `functionName` használatához az Azure-függvény tevékenységben `<functionName>/<value>`. Akkor is ezt a funkciót meg a kívánt paraméterezni `functionName` futásidőben.

Emellett támogatja az Azure-függvény tevékenység **lekérdezések**. A lekérdezés azt kell részét képezi a `functionName`. Például, ha a függvény nevét kell `HttpTriggerCSharp` és a lekérdezés, amely a felvenni kívánt `name=hello`, majd hozhatnak létre a `functionName` a Azure-függvény tevékenységben, `HttpTriggerCSharp?name=hello`. Ez a függvény rendelkeznek, az érték futásidőben lehet meghatározni.

## <a name="timeout-and-long-running-functions"></a>Időkorlát és a hosszú ideig futó függvények

Függetlenül attól, hogy 230 másodperc után az Azure Functions-időtúllépés történik a `functionTimeout` beállításaiban konfigurált beállítás. További információkért tekintse meg [ezt a cikket](../azure-functions/functions-versions.md#timeout). Ez a probléma megkerüléséhez kövesse az aszinkron minta, vagy használjon Durable Functions. Tartós függvények előnye, hogy így nem kell megvalósítani a saját saját állapot-követési mechanizmust kínálnak.

További információ a Durable Functions [Ez a cikk](../azure-functions/durable/durable-functions-overview.md). Beállíthat egy Azure-függvény tevékenység tartós függvény, amely választ küld egy másik URI-azonosítójú például [ebben a példában](../azure-functions/durable/durable-functions-http-api.md#http-api-url-discovery). Mivel `statusQueryGetUri` HTTP-állapot 202 közben a függvény fut, a webes tevékenység használatával lekérdezheti a állapotát, a függvény visszaadja. Egyszerűen állítsa be a webes tevékenységet a `url` mező értéke `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`. A tartós függvény lefutott, a függvény kimenetének lesznek a webes tevékenység kimenetét.


## <a name="next-steps"></a>További lépések

További tudnivalók a tevékenységek az adat-Előállítóhoz [az Azure Data Factory folyamatai és tevékenységei](concepts-pipelines-activities.md).
