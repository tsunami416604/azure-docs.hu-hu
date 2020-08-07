---
author: baanders
description: fájl belefoglalása az Azure Digital Twins útvonal-szűrési beállításaihoz
ms.service: digital-twins
ms.topic: include
ms.date: 8/3/2020
ms.author: baanders
ms.openlocfilehash: c5b78d80b1aa958af50b81dc152b4a746ce85f70
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87902196"
---
| Szűrő neve | Leírás | Szöveges séma szűrése | Támogatott értékek | 
| --- | --- | --- | --- |
| Igaz/hamis | Lehetővé teszi az útvonalak szűrés nélküli létrehozását, illetve az útvonal letiltását, így nincs esemény elküldése | `<true/false>` | `true`= az útvonal engedélyezve van szűrés nélkül <br> `false`= az útvonal le van tiltva |
| Típus | A digitális kettős példányon keresztül áramló [esemény típusa](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) | `type = '<eventType>'` | Itt láthatók a lehetséges eseménytípus értékei: <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Forrás | Az Azure Digital Twins-példány neve | `source = '<hostname>'`| Itt láthatja a lehetséges állomásnév-értékeket: <br> **Értesítésekhez**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **Telemetria esetén**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| Tárgy | Az esemény leírása a fenti eseményforrás kontextusában | `subject = '<subject>'` | Itt láthatók a lehetséges tárgyi értékek: <br>**Értesítésekhez**: a tulajdonos`<twinid>` <br> vagy a témák URI-formátuma, amelyeket a több rész vagy azonosító egyedileg azonosít:<br>`<twinid>/relationships/<relationshipid>`<br> **Telemetria esetén**: a tulajdonos az összetevő elérési útja (ha a telemetria egy Twin összetevőből származik), például: `comp1.comp2` . Ha a telemetria nem egy összetevőből származik, akkor a tárgy mezőjének üresnek kell lennie. |
| Adatséma | DTDL modell azonosítója | `dataschema = '<model-dtmi-ID>'` | **Telemetria esetében**: az Adatséma a Twin vagy a telemetria kibocsátó összetevő modell-azonosítója. Például: `dtmi:example:com:floor4;2` <br>**Értesítések esetén**: az Adatséma nem támogatott|
| Tartalomtípus | Adatérték tartalomtípusa | `datacontenttype = '<contentType>'` | A tartalom típusa`application/json` |
| Spec verziója | Az Ön által használt esemény-séma verziója | `specversion = '<version>'` | A verziónak a-nek kell lennie `1.0` . Ez a CloudEvents séma 1,0-es verzióját jelzi. |

A szűrők a következő műveletek használatával is kombinálhatók:

| Szűrő neve | Szöveges séma szűrése | Példa | 
| --- | --- | --- |
| ÉS/VAGY | `<filter1> AND <filter2>` | `type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'` |
| ÉS/VAGY | `<filter1> OR <filter2>` | `type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'` |
| Beágyazott műveletek | `(<Comparison1>) AND (<Comparison2>)` | `(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')` |

> [!NOTE]
> Az előzetes verzióban csak a karakterlánc-egyenlőség támogatott (=,! =).

Egy szűrő implementálása vagy frissítése során a módosítás néhány percet is igénybe vehet, hogy az adatfolyamatot tükrözze.
