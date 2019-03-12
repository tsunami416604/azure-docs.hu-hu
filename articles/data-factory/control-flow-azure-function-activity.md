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
ms.openlocfilehash: b98d20a1f96a6ab4a0dc72330e85fdc98ba04eae
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576378"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure-függvény tevékenység az Azure Data Factoryban

Az Azure-függvény tevékenység futtatását teszi [Azure Functions](../azure-functions/functions-overview.md) a Data Factory-folyamatok. Szeretne futtatni egy Azure-függvényt, szeretne létrehozni egy társított szolgáltatás kapcsolatot és a egy tevékenységgel, amely meghatározza az Azure-függvény, amely azt tervezi, hogy hajtsa végre.

Egy nyolc perces bevezető és a funkció bemutatójáért tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure-függvény társított szolgáltatás

Az Azure-függvény visszatérési típusával van, úgy, hogy érvényes `JObject`. (Vegye figyelembe, hogy [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) van *nem* egy `JObject`.) Más, a visszatérési típus `JObject` sikertelen lesz, és az általános felhasználói hiba lép fel a *hiba hívó végpont*.

| **Tulajdonság** | **Leírás** | **Kötelező** |
| --- | --- | --- |
| type   | A type tulajdonságot kell beállítani: **AzureFunction** | igen |
| függvény URL-címe | Az Azure-Függvényalkalmazás URL-címe. A formátum `https://<accountname>.azurewebsites.net`. Az URL-cím értéke alapján **URL-cím** az Azure Portalon a Függvényalkalmazás megtekintésekor szakasz  | igen |
| billentyűt | Az Azure-függvény hozzáférési kulcsát. Kattintson a a **kezelés** a megfelelő függvény szakaszt, és másolja vagy a **Függvénykulcs** vagy a **állomáskulcs**. Ismerje meg, itt: [Az Azure Functions – HTTP-eseményindítók és kötések](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | igen |
|   |   |   |

## <a name="azure-function-activity"></a>Azure-függvény tevékenység

| **Tulajdonság**  | **Leírás** | **Megengedett értékek** | **Kötelező** |
| --- | --- | --- | --- |
| név  | A folyamat a tevékenység neve  | String | igen |
| type  | A tevékenység típus "AzureFunctionActivity" | String | igen |
| Társított szolgáltatás | A társított Azure-függvény szolgáltatás a megfelelő Azure-függvényalkalmazás  | Társított szolgáltatás hivatkozása | igen |
| Függvény neve  | Az Azure-Függvényalkalmazást, amely meghívja ezt a tevékenységet a függvény neve | String | igen |
| method  | A függvény hívásához szükséges REST API-metódus | Karakterlánc típusok támogatottak: "GET", "POST", "PUT"   | igen |
| header  | A kérelmet küldött fejlécek. Például állítsa be a nyelvet, és írja be egy kérelemre: "fejlécek": {"Accept-nyelv": "en-us", "Content-Type": "application/json"} | Karakterlánc (vagy a resultType kifejezés karakterlánc) | Nem |
| törzs  | a függvény api-metódus a kéréssel együtt küldött törzs  | Karakterlánc (vagy a karakterlánc a resultType kifejezés) vagy az objektum.   | PUT/POST metódusok szükséges |
|   |   |   | |

A séma, a kérelem adattartalom [kérelem hasznos séma](control-flow-web-activity.md#request-payload-schema) szakaszban.

## <a name="more-info"></a>További információ

Az Azure-függvény tevékenység támogatja **útválasztási**. Például, ha az alkalmazás használja a következő útválasztás - `https://functionAPP.azurewebsites.net/api/functionName/{value}?code=<secret>` – a `functionName` van `functionName/{value}`, amelyeket meg lehet paraméterezni, adja meg a kívánt `functionName` futásidőben.

Emellett támogatja az Azure-függvény tevékenység **lekérdezések**. A lekérdezés részeként rendelkezik a `functionName` – például `HttpTriggerCSharp2?name=hello` – ahol az `function name` van `HttpTriggerCSharp2`.

## <a name="next-steps"></a>További lépések

További tudnivalók a tevékenységek az adat-Előállítóhoz [az Azure Data Factory folyamatai és tevékenységei](concepts-pipelines-activities.md).
