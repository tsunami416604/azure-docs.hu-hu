---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: e6dbfe225b6957406b7143d5db4a28cb50f13e7b
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55480643"
---
Az alábbi táblázat a különböző szolgáltatásszintek korlátait tartalmazza (S1, S2, S3, F1). Az egyes szintek *egységeihez* tartozó költségekről további információt az [IoT Hub Díjszabás](https://azure.microsoft.com/pricing/details/iot-hub/) című oldalon talál.

| Erőforrás | S1 – Standard | S2 – Standard | S3 – Standard | F1 – Ingyenes |
| --- | --- | --- | --- | --- |
| Üzenet/nap |400,000 |6,000,000 |300,000,000 |8,000 |
| Egységek maximális száma |200 |200 |10 |1 |

> [!NOTE]
> Ha egy S1 vagy S2 szintű Hubbal előreláthatóan 200-nál több, illetve egy S3 Hubbal 10-nél több egységet fog használni, lépjen kapcsolatba a Microsoft támogatási szolgálatával.
> 
> 

Az alábbi táblázat az IoT Hub-erőforrásokra vonatkozó korlátokat tartalmazza:

| Erőforrás | Korlát |
| --- | --- |
| Azure-előfizetésenkénti fizetős IoT Hubok maximális száma |50 |
| Azure-előfizetésenkénti ingyenes IoT Hubok maximális száma |1 |
| Egy eszközazonosítót karakterek maximális száma | 128 |
| Eszközidentitások egyetlen hívásban visszaadott<br/> maximális száma |1000 |
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
> Ha az Azure-előfizetéssel 50-nél több fizetős IoT hubra van szüksége, forduljon a Microsoft ügyfélszolgálatához.


> [!NOTE]
> Csatlakozhat egyetlen IoT hubra eszközök maximális számának jelenleg 1 000 000. Ha ezt a korlátot növelni szeretné, lépjen kapcsolatba [Support](https://azure.microsoft.com/support/options/).

Az IoT Hub szolgáltatás szabályozza a kéréseket az alábbi kvóták átlépése esetén:

| Szabályozás | Kötegenkénti érték |
| --- | --- |
| Identitásjegyzék műveletei <br/> (létrehozás, lekérdezés, listázás, frissítés, törlés), <br/> egyéni vagy tömeges importálás/exportálás |83.33/sec/Unit (5000/perc/egység) (az S3 esetén) <br/> 1.67/sec/Unit (100/perc/egység) (az S1 és S2 esetén). |
| Eszközkapcsolatok |6000/mp/egység (S3 esetén), 120/mp/egység (S2 esetén), 12/mp/egység (S1 esetén). <br/>Legalább 100/mp. |
| Az eszközről a felhőbe irányuló küldések |6000/mp/egység (S3 esetén), 120/mp/egység (S2 esetén), 12/mp/egység (S1 esetén). <br/>Legalább 100/mp. |
| Küldések a felhőből az eszközökre | 83.33/sec/Unit (5000/perc/egység) (S3 esetén), 1.67/sec/unit (100/perc/egység) (az S1 és S2 esetén). |
| Fogadások a felhőből az eszközökön |833.33/sec/Unit (50000/perc/egység) (S3 esetén), 16.67/sec/unit (1000/perc/egység) (S1 és S2 esetén). |
| Fájlfeltöltési műveletek |83.33 fájlfeltöltési értesítés/mp/egység (5000/perc/egység) (S3 esetén), 1.67 fájl feltöltése értesítések/mp/egység (100/perc/egység) (az S1 és S2 esetén). <br/> Egy Azure Storage-tárfiók esetén egyszerre 10000 SAS URI lehet használatban.<br/> Eszközönként egyszerre 10 SAS URI lehet használatban. |
| Közvetlen metódusok | 24MB/mp/egység (S3 esetén), 480KB/mp/egység (s2 esetén), 160KB/mp/egység (S1 esetén)<br/> A 8 KB-os szabályozás mérőszám mérete alapján. |
| Ikereszköz-olvasások | 50/mp/egység (S3 esetén), legfeljebb 10/mp vagy 1/mp/egység (S2 esetén), 10/mp (S1 esetén) |
| Ikereszköz-frissítések | 50/mp/egység (S3 esetén), legfeljebb 10/mp vagy 1/mp/egység (S2 esetén), 10/mp (S1 esetén) |
| Feladatműveletek <br/> (létrehozás, frissítés, listázás, törlés) | 83.33/sec/Unit (5000/perc/egység) (S3 esetén), 1.67/sec/unit (100/perc/egység) (az S2 esetén), 1.67/sec/unit (100/perc/egység) (az S1 esetén) |
| Feladatok eszközönkénti műveleti teljesítménye | 50/mp/egység (S3 esetén), legfeljebb 10/mp vagy 1/mp/egység (S2 esetén), 10/mp (S1 esetén) |
| Eszköz stream kezdeményezés sebessége | 5 új adatfolyamok/mp (S1, S2, S3, és csak az F1) |