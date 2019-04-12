---
title: Webhook-tevékenység az Azure Data Factoryban |} A Microsoft Docs
description: A Webhook tevékenység folytassa az a folyamat végrehajtását bizonyos feltételeknek, a felhasználó adja meg a csatolt adatkészlet ellenőrzi azt.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 6ec43b06ce266b9ceaddb5dd21cbf52f509d6596
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495908"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Webhook-tevékenység az Azure Data Factoryban
A web hook tevékenység segítségével szabályozhatja a folyamatok végrehajtásának az egyéni kódok segítségével. A webhook tevékenység használja, ügyfelek a végpont hívja, és adja át a visszahívási URL-címet. A folyamat futásának megvárja, amíg a következő tevékenységnek a folytatás előtt meg kell hívni a visszahívás.

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


## <a name="type-properties"></a>Tulajdonságok



Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
név | A web hook tevékenység neve | String | Igen |
type | Meg kell **WebHook**. | String | Igen |
method | A céloldali végpont metódus REST API-t. | karakterlánc. A támogatott típusok: "POST" | Igen |
url | Céloldali végpont és az elérési út | Karakterlánc (vagy a resultType kifejezés karakterlánc). | Igen |
A fejlécek | A kérelmet küldött fejlécek. Például állítsa be a nyelvet, és írja be egy kérelemre: "fejlécek": {"Accept-nyelv": "en-us", "Content-Type": "application/json"}. | Karakterlánc (vagy a resultType kifejezés karakterlánc) | Igen, a Content-type fejléc szükség. "headers":{ "Content-Type":"application/json"} |
törzs | A tartalom a végpontnak küldött jelöli. | A törzsben átadott vissza a visszahívás URI azonosítója nem egy érvényes JSON. A séma, a kérelem adattartalom [kérelem hasznos séma](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0) szakaszban. | Igen |
hitelesítés | A végpont meghívására szolgáló használt hitelesítési módszert. Támogatott típusok a következők "Alapszintű" vagy "ClientCertificate." További információkért lásd: [hitelesítési](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0) szakaszban. Ha a hitelesítés nem szükséges, zárja ki ezt a tulajdonságot. | Karakterlánc (vagy a resultType kifejezés karakterlánc) | Nem |
timeout | Mennyi ideig fog várni a tevékenység a &#39;visszahívási URI&#39; meg kell hívni. Mennyi ideig a tevékenység várakozik a "visszahívási URI" meg kell hívni. Alapértelmezett értéke 10mins ("00: 10:00"). Formátuma, azaz d.hh:mm:ss időtartam | String | Nem |

## <a name="additional-notes"></a>További megjegyzések

Az Azure Data Factory továbbítja egy további tulajdonságot "visszahívási URI" törzsében az URL-végpontot, és ez az uri a megadott időtúllépési érték előtt meg kell hívni fogja várható. Az URI-t nem hív, ha a tevékenység sikertelen "Időtúllépés" állapotú lesz.

A web hook tevékenységnél meghibásodik, csak ha az egyéni végpont hívása sikertelen. Bármilyen hibaüzenetet kap a visszahívás törzsébe hozzáadható és a soron következő tevékenysége használt.

## <a name="next-steps"></a>További lépések
Tekintse meg a többi Data Factory által támogatott átvitelvezérlési tevékenységek:

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
