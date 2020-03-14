---
title: Webhook-tevékenység Azure Data Factory
description: A webhook tevékenység nem folytatja a folyamat végrehajtását mindaddig, amíg a felhasználó által megadott feltételekkel ellenőrzi a csatolt adatkészletet.
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
ms.openlocfilehash: ced2279878ee2eb361ec7338647418658e411513
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79212998"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Webhook-tevékenység Azure Data Factory

A webhook-tevékenységek az Egyéni kódban vezérelhetik a folyamatok végrehajtását. A webhook tevékenységgel az ügyfelek kódja hívhat meg egy végpontot, és átadhatja a visszahívási URL-címet. A folyamat futása megvárja a visszahívás meghívását, mielőtt továbblép a következő tevékenységre.

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
**név** | A webhook tevékenység neve. | Sztring | Igen |
**type** | "Webhook" értékre kell beállítani. | Sztring | Igen |
**metódus** | A célként megadott végpont REST API metódusa. | sztring elemet. A támogatott típus a "POST". | Igen |
**URL** | A célként megadott végpont és elérési út. | Karakterlánc vagy kifejezés egy sztring **resultType** értékkel. | Igen |
**fejlécek** | A kérelembe küldendő fejlécek. Az alábbi példa egy kérelem nyelvét és típusát állítja be: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Karakterlánc vagy kifejezés egy sztring **resultType** értékkel. | Igen. `Content-Type` fejléc, például `"headers":{ "Content-Type":"application/json"}` szükséges. |
**törzse** | A végpontnak elküldhető adattartalmat jelöli. | Érvényes JSON vagy kifejezés a JSON **resultType** -értékkel. Lásd a kérelem hasznos adatainak sémájának [kérése adattartalom-sémáját](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#request-payload-schema) . | Igen |
**hitelesítés** | A végpont meghívásához használt hitelesítési módszer. A támogatott típusok az "alapszintű" és a "ClientCertificate". További információért lásd: [Hitelesítés](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#authentication). Ha nincs szükség hitelesítésre, zárja be ezt a tulajdonságot. | Karakterlánc vagy kifejezés egy sztring **resultType** értékkel. | Nem |
**időtúllépés** | Mennyi ideig várakozik a tevékenység a **callBackUri** által meghatározott visszahívás meghívásához. Az alapértelmezett érték 10 perc ("00:10:00"). Az értékek a *d*TimeSpan formátumban jelennek meg. *óó*:*PP*:*ss*mm. | Sztring | Nem |
**Jelentés állapota a visszahívás során** | Lehetővé teszi, hogy a felhasználó jelentést készítsen egy webhook-tevékenység sikertelen állapotáról. | Logikai | Nem |

## <a name="authentication"></a>Hitelesítés

A webhook-tevékenység a következő hitelesítési típusokat támogatja.

### <a name="none"></a>Nincsenek

Ha nincs szükség hitelesítésre, ne adja meg a **hitelesítési** tulajdonságot.

### <a name="basic"></a>Alapszintű

Az alapszintű hitelesítéshez használandó Felhasználónév és jelszó megadása.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Ügyféltanúsítvány

Itt adhatja meg a PFX-fájl és a jelszó Base64 kódolású tartalmát.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Felügyelt identitás

Az adatok gyárának felügyelt identitásával adhatja meg azt az erőforrás-URI-t, amelyhez a hozzáférési jogkivonatot kéri. Az Azure Resource Management API meghívásához használja a `https://management.azure.com/`. További információ a felügyelt identitások működéséről: [felügyelt identitások az Azure-erőforrások áttekintéséhez](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Ha az adat-előállító git-tárházral van konfigurálva, az alapszintű vagy az ügyféltanúsítvány-alapú hitelesítés használatához a hitelesítő adatait Azure Key Vault kell tárolnia. A Azure Data Factory nem tárol jelszavakat a git-ben.

## <a name="additional-notes"></a>További megjegyzések

Data Factory átadja az URL-végpontnak elküldett törzs további tulajdonságának **callBackUri** . A Data Factory a megadott időtúllépési érték előtt meghívja ezt az URI-t. Ha nem hívja meg az URI-t, a tevékenység a "időtúllépés" állapottal meghiúsul.

A webhook tevékenység meghiúsul, ha az egyéni végpontra irányuló hívás sikertelen. Bármely hibaüzenet felvehető a visszahívási törzsbe, és egy későbbi tevékenységben is felhasználható.

Minden REST API hívás esetén az ügyfél időtúllépést mutat, ha a végpont egy percen belül nem válaszol. Ez a szabványos HTTP ajánlott eljárás. A probléma megoldásához hozzon létre egy 202 mintát. Az aktuális esetben a végpont 202 (elfogadva) és az ügyfél-lekérdezéseket adja vissza.

A kérelemben szereplő egyperces időkorlát semmi köze a tevékenység időtúllépéséhez. Az utóbbi a **callbackUri**által meghatározott visszahívás megvárni.

A visszahívási URI-nak visszaadott törzsnek érvényes JSON-nek kell lennie. Állítsa a `Content-Type` fejlécet `application/json`re.

Ha a **jelentés állapotát visszahívási** tulajdonságot használja, a visszahíváshoz a következő kódot kell felvennie a törzsbe:

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

Tekintse meg a következő, a Data Factory által támogatott irányítási folyamatokat:

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
