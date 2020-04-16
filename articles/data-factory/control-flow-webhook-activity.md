---
title: Webhook-tevékenység az Azure Data Factoryban
description: A webhook-tevékenység nem folytatja a folyamat végrehajtását, amíg a felhasználó által megadott bizonyos feltételekkel nem ellenőrzi a csatolt adatkészletet.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 4056550ae0a71138d136878fc7e3aa5f6f8f4180
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417878"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Webhook-tevékenység az Azure Data Factoryban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A webhook-tevékenység vezérelheti a folyamatok végrehajtását az egyéni kódon keresztül. A webhook-tevékenység, az ügyfelek kódja hívhat meg egy végpontot, és adja át a visszahívási URL-t. A folyamat futtatása megvárja a visszahívásmeghívást, mielőtt a következő tevékenységre lépne.

## <a name="syntax"></a>Szintaxis

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
**név** | A webhook-tevékenység neve. | Sztring | Igen |
**Típus** | "WebHook" lesz. | Sztring | Igen |
**Módszer** | A CÉLvégpont REST API-metódusa. | Sztring. A támogatott típus a "POST". | Igen |
**Url** | A célvégpont és az elérési út. | Karakterlánc vagy kifejezés egy karakterlánc **resultType** értékével. | Igen |
**Fejlécek** | A kérésnek küldött fejlécek. Íme egy példa, amely beállítja a `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`nyelvet és a beírt kéréshez: . | Karakterlánc vagy kifejezés egy karakterlánc **resultType** értékével. | Igen. Egy `Content-Type` ilyen `"headers":{ "Content-Type":"application/json"}` fejléc szükséges. |
**Szervezet** | A végpontra küldött hasznos adat. | Érvényes JSON vagy egy kifejezés, amelynek **resultType** értéke JSON. Lásd: [Hasznos séma kérése](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#request-payload-schema) a kérelem hasznos adatának sémájához. | Igen |
**Hitelesítés** | A végpont hívásához használt hitelesítési módszer. A támogatott típusok az "Alapszintű" és a "ClientCertificate". További információért lásd: [Hitelesítés](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#authentication). Ha nincs szükséges hitelesítés, zárja ki ezt a tulajdonságot. | Karakterlánc vagy kifejezés egy karakterlánc **resultType** értékével. | Nem |
**timeout** | Mennyi ideig vár a tevékenység a **callBackUri** által megadott visszahívás meghívására. Az alapértelmezett érték 10 perc ("00:10:00"). Az értékek TimeSpan *formátumúak d*. *ó*:*mm*:*ss*. | Sztring | Nem |
**Jelentés állapota visszahívásközben** | Lehetővé teszi, hogy egy felhasználó jelentse a webhook-tevékenység sikertelen állapotát. | Logikai | Nem |

## <a name="authentication"></a>Hitelesítés

A webhook-tevékenység a következő hitelesítési típusokat támogatja.

### <a name="none"></a>None

Ha nincs szükséges hitelesítés, ne adja meg a **hitelesítési** tulajdonságot.

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

Adja meg a PFX-fájl és a jelszó Base64 kódolású tartalmát.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Felügyelt identitás

Az adat-előállító felügyelt identitása segítségével adja meg azt az erőforrás URI-t, amelyhez a hozzáférési jogkivonatot kéri. Az Azure Resource Management API `https://management.azure.com/`hívásához használja a használatát. A felügyelt identitások működéséről az [Azure-erőforrások felügyelt identitások áttekintése című témakörben olvashat bővebben.](/azure/active-directory/managed-identities-azure-resources/overview)

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Ha az adatgyár git-tárházzal van konfigurálva, az alapszintű vagy ügyféltanúsítvány-hitelesítés használatához az Azure Key Vaultban kell tárolnia a hitelesítő adatait. Az Azure Data Factory nem tárolja a jelszavakat a Gitben.

## <a name="additional-notes"></a>További megjegyzések

A Data Factory továbbítja a további **tulajdonság callBackUri** a szervezetben küldött az URL-végpontra. A Data Factory azt várja, hogy ezt az URI-t a megadott időtúlérték előtt kell meghívni. Ha az URI nem hívható meg, a tevékenység sikertelen állapotú "TimedOut".

A webhook-tevékenység sikertelen, ha az egyéni végpont hívása sikertelen. Bármilyen hibaüzenet hozzáadható a visszahívási szervezethez, és felhasználható egy későbbi tevékenységben.

Minden REST API-hívás esetén az ügyfél időtúlmúlása, ha a végpont nem válaszol egy percen belül. Ez a viselkedés a http-vel kapcsolatos szokásos ajánlott eljárás. A probléma megoldásához valósítson meg egy 202-es mintát. Az aktuális esetben a végpont 202 (Elfogadva) értéket ad vissza, és az ügyfél lekérdezi.

A kérelem egyperces időhahívásának semmi köze a tevékenység időhöz. Ez utóbbi a **callbackUri**által megadott visszahívásra vár.

A visszahívási URI-nak visszaadott törzsnek érvényes JSON-nak kell lennie. Állítsa `Content-Type` a `application/json`fejlécet a beállításra.

Ha a **Visszahívás i állapotát** használja, a visszahívás során a következő kódot kell hozzáadnia a törzshöz:

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>További lépések

Tekintse meg a Data Factory által támogatott következő vezérlési folyamattevékenységeket:

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
