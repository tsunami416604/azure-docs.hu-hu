---
title: Webhook-tevékenység a Azure Data Factoryban | Microsoft Docs
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
ms.openlocfilehash: c11fb800dba06ab5566647489f020f727860a7ff
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142408"
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
name | A webhook-tevékenység neve | Sztring | Igen |
type | Webhookra kellbeállítani. | Sztring | Igen |
metódus | A célként megadott végpont REST API-metódusa. | Karakterlánc. Támogatott típusok: UTÁNI | Igen |
url | Cél végpontja és elérési útja | Karakterlánc (vagy resultType karakterláncot tartalmazó kifejezés). | Igen |
fejlécek | A kérelembe küldendő fejlécek. Például a nyelv és a típus megadásához a következőre: "headers": {"Accept-Language": "en-us", "Content-Type": "Application/JSON"}. | Karakterlánc (vagy resultType karakterláncot tartalmazó kifejezés) | Igen, a Content-Type fejléc megadása kötelező. "headers":{ "Content-Type":"application/json"} |
törzs | A végpontnak elküldhető adattartalmat jelöli. | A visszahívási URI-nak visszaadott törzsnek érvényes JSON-nek kell lennie. Tekintse meg a kérelem hasznos adatainak sémáját a [kérelmek hasznos](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0) adatait tartalmazó sémában. | Igen |
hitelesítés | A végpont meghívásához használt hitelesítési módszer. A támogatott típusok az "alapszintű" vagy a "ClientCertificate". További információ: [hitelesítés](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0) szakasz. Ha nincs szükség hitelesítésre, zárja be ezt a tulajdonságot. | Karakterlánc (vagy resultType karakterláncot tartalmazó kifejezés) | Nem |
timeout | Mennyi ideig várakozik a tevékenység a &#39;callBackUri&#39; meghívása során. Mennyi ideig várakozik a tevékenység a "callBackUri" meghívása után. Az alapértelmezett érték a 10mins ("00:10:00"). A formátum TimeSpan, azaz d. óó: PP: SS | Sztring | Nem |

## <a name="additional-notes"></a>További megjegyzések

A Azure Data Factory egy további "callBackUri" tulajdonságot fog átadni a törzsben az URL-végpontnak, és a megadott időtúllépési érték előtt meghívja ezt az URI-t. Ha a rendszer nem hívja meg az URI-t, a tevékenység sikertelen lesz a következő állapottal: "időtúllépés".

A webhook-tevékenység maga a művelet csak akkor meghiúsul, ha az egyéni végpontra irányuló hívás sikertelen. Bármilyen hibaüzenetet hozzáadhat a visszahívás törzséhez, és egy későbbi tevékenységben is felhasználhatja őket.

## <a name="next-steps"></a>További lépések
Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamatokat:

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
