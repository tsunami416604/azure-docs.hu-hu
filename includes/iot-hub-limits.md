---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 34de38e91d47457d215c7ebf65d04ed2dbae5324
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74224418"
---
Az alábbi táblázat az S1, S2, S3 és F1 szolgáltatásszintekhez kapcsolódó korlátokat sorolja fel. Az egyes szintek egyes *egységeinek* költségéről az [Azure IoT Hub díjszabása](https://azure.microsoft.com/pricing/details/iot-hub/)című témakörben talál további információt.

| Erőforrás | S1 – Standard | S2 – Standard | S3 – Standard | F1 – Ingyenes |
| --- | --- | --- | --- | --- |
| Üzenet/nap |400,000 |6,000,000 |300,000,000 |8,000 |
| Egységek maximális száma |200 |200 |10 |1 |

> [!NOTE]
> Ha várhatóan több mint 200 egységet használ egy S1 vagy S2 szintű hubbal, vagy 10 egységet egy S3 szintű hubbal, forduljon a Microsoft támogatási szolgálatához.
> 
> 

Az alábbi táblázat az IoT Hub-erőforrásokra vonatkozó korlátokat sorolja fel.

| Erőforrás | Korlát |
| --- | --- |
| Azure-előfizetésenkénti fizetős IoT Hubok maximális száma |100 |
| Azure-előfizetésenkénti ingyenes IoT Hubok maximális száma |1 |
| Az eszközazonosítóban szereplő karakterek maximális száma | 128 |
| Eszközidentitások egyetlen hívásban visszaadott<br/> maximális száma |1,000 |
| Egy eszközről a felhőbe irányuló IoT Hub-üzenetek maximális megőrzési ideje |7 nap |
| Egy eszközről a felhőbe irányuló üzenet maximális mérete |256 KB |
| Egy eszközről a felhőbe irányuló köteg maximális mérete |AMQP és HTTP: 256 KB a teljes kötegre <br/>MQTT: 256 KB minden üzenethez |
| Egy eszközről a felhőbe irányuló kötegben található üzenetek maximális száma |500 |
| A felhőből egy eszközre irányuló üzenet maximális mérete |64 KB |
| A felhőből egy eszközre irányuló üzenetek maximális élettartama |2 nap |
| A felhőből egy eszközre történő kézbesítések maximális száma <br/> üzenet |100 |
| A felhőből az eszközre irányuló várólista maximális mélysége eszközönként |50 |
| A felhőből egy eszközre irányuló üzenetre válaszoló <br/> visszajelzési üzenetek maximális kézbesítésszáma |100 |
| A felhőből egy eszközre irányuló üzenetre válaszoló <br/> visszajelzési üzenetek maximális élettartama |2 nap |
| [Ikereszköz maximális mérete](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB a címkék szakasz, és 32 KB a kívánt és jelentett tulajdonságok szakaszok minden |
| Az ikerkarakterlánc-kulcs maximális hossza | 1 KB |
| Az ikerkarakterlánc-érték maximális hossza | 4 KB |
| [Az ikereszközben található objektum maximális mélysége](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| A közvetlen metódus hasznos adatainak maximális mérete | 128 KB |
| Feladatelőzmények maximális megőrzési ideje | 30 nap |
| Egyidejű feladatok maximális száma | 10 (S3 esetén), 5 (S2 esetén), 1 (S1 esetén) |
| További végpontok maximális száma | 10 (S1, S2 és S3 esetén) |
| Üzenet-útválasztási szabályok maximális száma | 100 (S1, S2 és S3 esetén) |
| Egyidejűleg csatlakoztatott eszközadatfolyamok maximális száma | 50 (csak S1, S2, S3 és F1 esetén) |
| Az eszközadatfolyam-átvitel maximális | 300 MB naponta (csak S1, S2, S3 és F1 esetén) |

> [!NOTE]
> Ha egy Azure-előfizetésben több mint 100 fizetős IoT-központra van szüksége, forduljon a Microsoft támogatási szolgálatához.

> [!NOTE]
> Jelenleg az eszközök és az egyetlen IoT hubra regisztrálható modulok teljes száma 1 000 000. Ha növelni szeretné ezt a korlátot, forduljon [a Microsoft támogatási szolgálatához.](https://azure.microsoft.com/support/options/)

Az IoT Hub a következő kvóták túllépése esetén szabályozza a kérelmeket.

| Szabályozás | Kötegenkénti érték |
| --- | --- |
| Identitásjegyzék műveletei <br/> (létrehozás, beolvasás, lista, frissítés és törlés), <br/> egyéni vagy tömeges importálás/exportálás |83,33/mp/egység (5000/perc/egység) (S3 esetén). <br/> 1,67/mp/egység (100/perc/egység) (S1 és S2 esetén). |
| Eszközkapcsolatok |6000/mp/egység (S3 esetén), 120/mp/egység (S2 esetén), 12/mp/egység (S1 esetén). <br/>Legalább 100/mp. |
| Az eszközről a felhőbe irányuló küldések |6000/mp/egység (S3 esetén), 120/mp/egység (S2 esetén), 12/mp/egység (S1 esetén). <br/>Legalább 100/mp. |
| Küldések a felhőből az eszközökre | 83,33/mp/egység (5000/perc/egység) (S3 esetén), 1,67/mp/egység (100/perc/egység) (S1 és S2 esetén). |
| Fogadások a felhőből az eszközökön |833,33/mp/egység (50 000/perc/egység) (S3 esetén), 16,67/mp/egység (1000/perc/egység) (S1 és S2 esetén). |
| Fájlfeltöltési műveletek |83.33 fájlfeltöltési kezdeményezés/mp/egység (5000/perc/egység) (S3 esetén), 1,67 fájlfeltöltési kezdeményezés/mp/egység (100/perc/egység) (S1 és S2 esetén). <br/> 10 000 SAS URI-k lehet ki egy Azure Storage-fiók egyszerre.<br/> Eszközönként egyszerre 10 SAS URI lehet használatban. |
| Közvetlen metódusok | 24 MB/mp/egység (S3 esetén), 480 KB/mp/egység (S2 esetén), 160 KB/mp/egység (S1 esetén).<br/> 8 KB-os sávszélesség-szabályozásmérő mérete alapján. |
| Ikereszköz-olvasások | 500/mp/egység (S3 esetén), legfeljebb 100/mp vagy 10/mp/egység (S2 esetén), 100/mp (S1 esetén) |
| Ikereszköz-frissítések | 250/mp/egység (S3 esetén), maximum 50/mp vagy 5/mp/egység (S2 esetén), 50/mp (S1 esetén) |
| Feladatműveletek <br/> (létrehozás, frissítés, lista és törlés) | 83,33/mp/egység (5000/perc/egység) (S3 esetén), 1,67/mp/egység (100/perc/egység) (S2 esetén), 1,67/mp/egység (100/perc/egység) (S1 esetén). |
| Feladatok eszközönkénti műveleti teljesítménye | 50/mp/egység (S3 esetén), legfeljebb 10/mp vagy 1/mp/egység (S2 esetén), 10/mp (S1 esetén). |
| Eszközfolyam-kezdeményezési sebesség | 5 új adatfolyam/mp (csak S1, S2, S3 és F1 esetén). |
