---
title: Azure-függvény tevékenység az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure-függvény tevékenység egy Azure-függvényt a Data Factory-folyamatok futtatásához
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: douglasl
ms.openlocfilehash: ef93c62a2e2084a43eeda578c889a568d04db4f1
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856418"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure-függvény tevékenység az Azure Data Factoryban

Az Azure-függvény tevékenység futtatását teszi [Azure Functions](../azure-functions/functions-overview.md) a Data Factory-folyamatok. Szeretne futtatni egy Azure-függvényt, szeretne létrehozni egy társított szolgáltatás kapcsolatot és a egy tevékenységgel, amely meghatározza az Azure-függvény, amely azt tervezi, hogy hajtsa végre.

## <a name="azure-function-linked-service"></a>Azure-függvény társított szolgáltatás

| **Tulajdonság** | **Leírás** | **Szükséges** |
| --- | --- | --- |
| type   | A type tulajdonság értékre kell állítani: **AzureFunction** | igen |
| függvény URL-címe | Az Azure-Függvényalkalmazás URL-címe. A formátum `https://<accountname>.azurewebsites.net`. Az URL-cím értéke alapján **URL-cím** az Azure Portalon a Függvényalkalmazás megtekintésekor szakasz  | igen |
| billentyűt | Az Azure-függvény hozzáférési kulcsát. Kattintson a a **kezelés** a megfelelő függvény szakaszt, és másolja vagy a **Függvénykulcs** vagy a **állomáskulcs**. Ismerje meg a további itt: [Azure Functions – HTTP-eseményindítók és kötések](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | igen |
|   |   |   |

## <a name="azure-function-activity"></a>Azure-függvény tevékenység

| **Tulajdonság**  | **Leírás** | **Megengedett értékek** | **Szükséges** |
| --- | --- | --- | --- |
| név  | A folyamat a tevékenység neve  | Karakterlánc | igen |
| type  | A tevékenység típus "AzureFunctionActivity" | Karakterlánc | igen |
| Társított szolgáltatás | A társított Azure-függvény szolgáltatás a megfelelő Azure-függvényalkalmazás  | Társított szolgáltatás hivatkozása | igen |
| Függvény neve  | Az Azure-Függvényalkalmazást, amely meghívja ezt a tevékenységet a függvény neve | Karakterlánc | igen |
| method  | A függvény hívásához szükséges REST API-metódus | Karakterlánc támogatott típusok: "GET", "Közzététel", "PUT"   | igen |
| header  | A kérelmet küldött fejlécek. Például állítsa be a nyelvet, és írja be egy kérelemre: "fejlécek": {"Accept-nyelv": "en-us", "Content-Type": "application/json"} | Karakterlánc (vagy a resultType kifejezés karakterlánc) | Nem |
| Törzs  | a függvény api-metódus a kéréssel együtt küldött törzs  | Karakterlánc (vagy a resultType kifejezés karakterlánc).   | PUT/POST metódusok szükséges |
|   |   |   | |

A séma, a kérelem adattartalom [kérelem hasznos séma](control-flow-web-activity.md#request-payload-schema) szakaszban.

## <a name="next-steps"></a>További lépések

További tudnivalók a tevékenységek az adat-Előállítóhoz [az Azure Data Factory folyamatai és tevékenységei](concepts-pipelines-activities.md).