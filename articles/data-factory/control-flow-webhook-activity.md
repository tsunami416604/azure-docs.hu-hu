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
ms.openlocfilehash: 70c67a99274eaedc5592c7b90b1ef80a3a17acf8
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109998"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Webhook-tevékenység Azure Data Factory
Webhook-tevékenységgel vezérelheti a folyamatok végrehajtását az egyéni kóddal. A webhook tevékenység használatával az ügyfelek meghívhatnak egy végpontot, és átadhatják a visszahívási URL-címet. A folyamat futása megvárja a visszahívás meghívását, mielőtt továbblép a következő tevékenységre.

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
név | A webhook-tevékenység neve | Sztring | Igen |
type | **Webhookra**kell beállítani. | Sztring | Igen |
method | A célként megadott végpont REST API-metódusa. | sztring elemet. Támogatott típusok: "POST" | Igen |
url | Cél végpontja és elérési útja | Karakterlánc (vagy resultType karakterláncot tartalmazó kifejezés). | Igen |
fejlécek | A kérelembe küldendő fejlécek. Például a nyelv és a típus megadásához a következőre: "headers": {"Accept-Language": "en-us", "Content-Type": "Application/JSON"}. | Karakterlánc (vagy resultType karakterláncot tartalmazó kifejezés) | Igen, a Content-Type fejléc megadása kötelező. "headers":{ "Content-Type":"application/json"} |
törzse | A végpontnak elküldhető adattartalmat jelöli. | Érvényes JSON (vagy kifejezés a JSON-resultType). Tekintse meg a kérelem hasznos adatainak sémáját a [kérelmek hasznos adatait tartalmazó sémában](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0) . | Igen |
hitelesítés | A végpont meghívásához használt hitelesítési módszer. A támogatott típusok az "alapszintű" vagy a "ClientCertificate". További információ: [hitelesítés](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0) szakasz. Ha nincs szükség hitelesítésre, zárja be ezt a tulajdonságot. | Karakterlánc (vagy resultType karakterláncot tartalmazó kifejezés) | Nem |
timeout | Mennyi ideig várakozik a tevékenység a &#39;callBackUri&#39; meghívása során. Mennyi ideig várakozik a tevékenység a "callBackUri" meghívása után. Az alapértelmezett érték a 10mins ("00:10:00"). A formátum TimeSpan, azaz d. óó: PP: SS | Sztring | Nem |
Jelentés állapota a visszahívás során | Lehetővé teszi, hogy a felhasználó jelentést készítsen a webhook tevékenység sikertelen állapotáról, amely sikertelenként jelöli meg a tevékenységet. | Logikai | Nem |

## <a name="authentication"></a>Authentication

Alább láthatók a webhook tevékenységben támogatott hitelesítési típusok.

### <a name="none"></a>Nincs

Ha nincs szükség hitelesítésre, ne adja meg a "hitelesítés" tulajdonságot.

### <a name="basic"></a>Alapszintű

Adja meg az alapszintű hitelesítéshez használni kívánt felhasználónevet és jelszót.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Ügyféltanúsítvány

A PFX-fájl és a jelszó Base64 kódolású tartalmának megadása.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Felügyelt identitás

Itt adhatja meg azt az erőforrás-URI-t, amelynek a hozzáférési jogkivonatát a rendszer az adatok előállítójának felügyelt identitása alapján kéri le. Az Azure Resource Management API meghívásához használja a `https://management.azure.com/`. További információ a felügyelt identitások működéséről: [felügyelt identitások az Azure-erőforrások áttekintéséhez](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Ha az adat-előállító git-tárházral van konfigurálva, az alapszintű vagy az ügyféltanúsítvány-alapú hitelesítés használatához a hitelesítő adatait Azure Key Vault kell tárolnia. A Azure Data Factory nem tárol jelszavakat a git-ben.

## <a name="additional-notes"></a>További megjegyzések

A Azure Data Factory egy további "callBackUri" tulajdonságot fog átadni a törzsben az URL-végpontnak, és a megadott időtúllépési érték előtt meghívja ezt az URI-t. Ha a rendszer nem hívja meg az URI-t, a tevékenység sikertelen lesz a következő állapottal: "időtúllépés".

Maga a webhook tevékenység meghiúsul, ha az egyéni végpontra irányuló hívás sikertelen. Bármilyen hibaüzenetet hozzáadhat a visszahívás törzséhez, és egy későbbi tevékenységben is felhasználhatja őket.

A visszahívási URI-nak visszaadott törzsnek érvényes JSON-nek kell lennie. A Content-type fejlécet `application/json`re kell állítania.

Ha a "jelentés állapota visszahíváskor" beállítást használja, az alábbi kódrészletet hozzá kell adnia a törzshez a visszahívás elkészítésekor:

```
{
    "Output": {
        // output object will be used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity will be marked as failed
}
```



## <a name="next-steps"></a>Következő lépések

Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamatokat:

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
