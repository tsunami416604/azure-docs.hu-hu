---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 1bdf73dc6a4edf0c170b51e70fca2128d22e0eb8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804175"
---
Az alábbi táblázat a különböző szolgáltatásszinten, S1, S2, S3 és F1 korlátait. A költségek információ az egyes *egység* tekintse meg az egyes szintek [Azure IoT Hub díjszabás](https://azure.microsoft.com/pricing/details/iot-hub/).

| Erőforrás | S1 – Standard | S2 – Standard | S3 – Standard | F1 – Ingyenes |
| --- | --- | --- | --- | --- |
| Üzenet/nap |400,000 |6,000,000 |300,000,000 |8,000 |
| Egységek maximális száma |200 |200 |10 |1 |

> [!NOTE]
> Ha várhatóan több mint 200 egységre lenne szüksége egy S1 vagy S2 szintű hubbal és a egy S3 szintű hubbal 10 egység használata, forduljon a Microsoft Support.
> 
> 

Az alábbi táblázat az IoT Hub-erőforrásokra vonatkozó korlátokat.

| Erőforrás | Korlát |
| --- | --- |
| Azure-előfizetésenkénti fizetős IoT Hubok maximális száma |50 |
| Azure-előfizetésenkénti ingyenes IoT Hubok maximális száma |1 |
| Egy Eszközazonosítót karakterek maximális száma | 128 |
| Eszközidentitások egyetlen hívásban visszaadott<br/> maximális száma |1,000 |
| Egy eszközről a felhőbe irányuló IoT Hub-üzenetek maximális megőrzési ideje |7 nap |
| Egy eszközről a felhőbe irányuló üzenet maximális mérete |256 KB |
| Egy eszközről a felhőbe irányuló köteg maximális mérete |Amqp-t és HTTP: Az egész köteget a 256 KB <br/>MQTT-RŐL: Az egyes üzenetekhez 256 KB |
| Egy eszközről a felhőbe irányuló kötegben található üzenetek maximális száma |500 |
| A felhőből egy eszközre irányuló üzenet maximális mérete |64 KB |
| A felhőből egy eszközre irányuló üzenetek maximális élettartama |2 nap |
| A felhőből egy eszközre történő kézbesítések maximális száma <br/> üzenet |100 |
| A felhőből egy eszközre irányuló üzenetre válaszoló <br/> visszajelzési üzenetek maximális kézbesítésszáma |100 |
| A felhőből egy eszközre irányuló üzenetre válaszoló <br/> visszajelzési üzenetek maximális élettartama |2 nap |
| Ikereszköz maximális mérete <br/> (címkék, jelentett tulajdonságok és kívánt tulajdonságok) | 8 KB |
| Az ikereszköz sztringértékének maximális mérete | 4 KB |
| Az ikereszközben található objektum maximális mélysége | 5 |
| A közvetlen metódus hasznos adatainak maximális mérete | 128 KB |
| Feladatelőzmények maximális megőrzési ideje | 30 nap |
| Egyidejű feladatok maximális száma | 10 (S3 esetén), 5 (S2 esetén), 1 (S1 esetén) |
| További végpontok maximális száma | 10 (S1, S2 és S3 esetén) |
| Üzenet-útválasztási szabályok maximális száma | 100 (S1, S2 és S3 esetén) |
| Egyidejűleg csatlakoztatott eszköz adatfolyamok maximális száma | 50 (az S1, S2, S3, és csak az F1) |
| Maximális eszköz stream adatátvitel | 300 MB / nap (az S1, S2, S3, és csak az F1) |


> [!NOTE]
> Ha az Azure-előfizetéssel 50-nél több fizetős IoT hubra van szüksége, forduljon a Microsoft Support.


> [!NOTE]
> Csatlakozhat egyetlen IoT hubra eszközök maximális számának jelenleg 1 000 000. Ha ezt a korlátot növelni szeretné, lépjen kapcsolatba [Support](https://azure.microsoft.com/support/options/).

Az IoT Hub szabályozza a kéréseket, amikor az alábbi kvóták átlépése.

| Szabályozás | Kötegenkénti érték |
| --- | --- |
| Identitásjegyzék műveletei <br/> (létrehozása, beolvasása, listázása, frissítése és törlése), <br/> egyéni vagy tömeges importálás/exportálás |83.33/sec/Unit (5000/perc/egység) (az S3 esetén). <br/> 1.67/sec/Unit (100/perc/egység) (az S1 és S2 esetén). |
| Eszközkapcsolatok |6000/mp/egység (S3 esetén), 120/mp/egység (s2 esetén), 12/mp/egység (S1 esetén). <br/>Legalább 100/mp. |
| Az eszközről a felhőbe irányuló küldések |6000/mp/egység (S3 esetén), 120/mp/egység (s2 esetén), 12/mp/egység (S1 esetén). <br/>Legalább 100/mp. |
| Küldések a felhőből az eszközökre | 83.33/sec/Unit (5000/perc/egység) (S3 esetén), 1.67/sec/unit (100/perc/egység) (az S1 és S2 esetén). |
| Fogadások a felhőből az eszközökön |833.33/sec/Unit (50 000/perc/egység) (S3 esetén), 16.67/sec/unit (1000/perc/egység) (az S1 és S2 esetén). |
| Fájlfeltöltési műveletek |83.33 fájlfeltöltési értesítés/mp/egység (5000/perc/egység) (S3 esetén), 1.67 fájl feltöltése értesítések/mp/egység (100/perc/egység) (az S1 és S2 esetén). <br/> 10 000 SAS URI lehet az Azure Storage-fiók egy időben.<br/> Eszközönként egyszerre 10 SAS URI lehet használatban. |
| Közvetlen metódusok | 24 MB/mp/egység (S3 esetén), 480 KB/s/egység (s2 esetén), 160 KB/s/egység (S1 esetén).<br/> 8 KB-os sávszélesség-szabályozási mérőszám mérete alapján. |
| Ikereszköz-olvasások | 500-as/mp/egység (S3 esetén), legfeljebb 100/mp és 10/mp/egység (s2 esetén), 100/mp (S1 esetén) |
| Ikereszköz-frissítések | 250/mp/egység (S3 esetén), legfeljebb 50/mp vagy 5/mp/egység (s2 esetén), 50/mp (S1 esetén) |
| Feladatműveletek <br/> (létrehozása, lista, frissítése és törlése) | 83.33/sec/Unit (5000/perc/egység) (S3 esetén), 1.67/sec/unit (100/perc/egység) (az S2 esetén), 1.67/sec/unit (100/perc/egység) (az S1 esetén). |
| Feladatok eszközönkénti műveleti teljesítménye | 50/mp/egység (S3 esetén), legfeljebb 10/mp vagy 1/mp/egység (s2 esetén), 10/mp (S1 esetén). |
| Eszköz stream kezdeményezés sebessége | 5 új adatfolyamok/mp (S1, S2, S3, és csak az F1). |
