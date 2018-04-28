---
title: Azure IoT Hub kvóták megértéséhez, valamint a sávszélesség-szabályozás |} Microsoft Docs
description: Fejlesztői útmutató – az IoT-központ és az elvárt szabályozási viselkedés vonatkozó kvótákat leírása.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 425e1b08-8789-4377-85f7-c13131fae4ce
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: dobett
ms.openlocfilehash: 37d1397b0601e09b12c0c05ff0adc6a916d66d70
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Referencia - IoT-központ kvóták és sávszélesség-szabályozás

## <a name="quotas-and-throttling"></a>Kvóták és szabályozás
Minden Azure-előfizetéssel, legfeljebb 10 IoT-központok, és legfeljebb 1 szabad hub rendelkezhet.

Minden egyes IoT-központ ki van építve bizonyos számú egység egy konkrét csomagot kiválasztani. További információkért lásd: [Azure IoT Hub árképzési][lnk-pricing]. A réteg és egységek számát határozza meg, küldhet üzeneteket napi kvóta felső határát.

A réteg a szabályozási korlát az összes művelet kikényszeríti az IoT-központ azt is meghatározza.

## <a name="operation-throttles"></a>A művelet szabályozások
Művelet szabályozások percben tartományok lesznek alkalmazva, és célja, hogy visszaélés megakadályozására arány korlátozások is. Az IoT-központ ad vissza, amikor csak lehetséges hibák elkerülése érdekében megkísérli, de elindítja a kivételek ad vissza, ha a késleltetési túl sokáig sérül.

Egy adott időpontban növelhető kvótákat vagy korlátozásokat késleltetési által kiosztott egység az IoT-központ számának növelése.

A következő táblázat a kényszerített szabályozások. Értékek egyes hubhoz hivatkozik.

| Szabályozás | Ingyenes, B1 és S1 | K2 és S2 | B3 és S3 | 
| -------- | ------- | ------- | ------- |
| Identitás kapcsolatos műveletek (létrehozása, beolvasása, listázása, frissítése és törlése) | 1.67/sec/Unit (100/perc/egység) | 1.67/sec/Unit (100/perc/egység) | 83.33/sec/Unit (5000/perc/egység) |
| Eszközkapcsolatok | Nagyobb 100 másodpercenkénti vagy 12/mp/egység <br/> Például két S1 egység: 2\*12 = 24 vagy mp, de legalább 100/mp a egységek között. Kilenc S1 egység, az informatikai részleg 108/mp (9\*12) között a egységeket. | 120/mp/egység | 6000/mp/egység |
| Az eszközről a felhőbe irányuló küldések | Nagyobb 100 másodpercenkénti vagy 12/mp/egység <br/> Például két S1 egység: 2\*12 = 24 vagy mp, de legalább 100/mp a egységek között. Kilenc S1 egység, az informatikai részleg 108/mp (9\*12) között a egységeket. | 120/mp/egység | 6000/mp/egység |
| Felhő eszközre elküldi<sup>1</sup> | 1.67/sec/Unit (100/perc/egység) | 1.67/sec/Unit (100/perc/egység) | 83.33/sec/Unit (5000/perc/egység) |
| Felhő-eszköz megkapja<sup>1</sup> <br/> (csak ha eszköz HTTPS PROTOKOLLT használ)| 16.67/sec/Unit (1000/perc/egység) | 16.67/sec/Unit (1000/perc/egység) | 833.33/sec/Unit (50000/perc/egység) |
| Fájl feltöltése | értesítések/mp/egység 1.67 fájl feltöltése (100/perc/egység) | értesítések/mp/egység 1.67 fájl feltöltése (100/perc/egység) | értesítések/mp/egység 83.33 fájl feltöltése (5000/perc/egység) |
| Közvetlen módszerek<sup>1</sup> | 160KB/sec/unit<sup>2</sup> | 480KB/sec/unit<sup>2</sup> | 24MB/s/egység<sup>2</sup> | 
| Kettős (eszköz- és modul) olvasási<sup>1</sup> | 10/mp | Magasabb 10 másodpercenként vagy 1/mp/egység | 50/mp/egység |
| Kettős (eszköz és modul)<sup>1</sup> | 10/mp | Magasabb 10 másodpercenként vagy 1/mp/egység | 50/mp/egység |
| Operatív feladatok<sup>1</sup> <br/> (létrehozás, frissítés, listázás, törlés) | 1.67/sec/Unit (100/perc/egység) | 1.67/sec/Unit (100/perc/egység) | 83.33/sec/Unit (5000/perc/egység) |
| Feladatok eszközönkénti művelet átviteli<sup>1</sup> | 10/mp | Magasabb 10 másodpercenként vagy 1/mp/egység | 50/mp/egység |

<sup>1</sup>a szolgáltatás nem érhető el az IoT-központ alapszintű rétegben. További információkért lásd: [kiválasztása a megfelelő IoT-központ](iot-hub-scaling.md). <br/><sup>2</sup>8 KB-os sávszélesség-szabályozás mérő mérete.

A *eszközcsatlakozás* késleltetési szabályozza a sebesség, amellyel új eszköz kapcsolatok hozhatók létre és az IoT-központ. A *eszközcsatlakozás* késleltetési nem szabályozza a egyidejűleg csatlakoztatott eszközök maximális számát. A szabályozási attól függ, hogy az IoT hub kiépített egységek száma.

Például ha vásárol S1 egyetlen egységben, kap egy 100-kapcsolatok a késleltetési. Ezért 100 000 eszközt a csatlakozáshoz tart legalább 1 000 másodperc (körülbelül 16 perc). Rendelkezik a identitásjegyzékhez-ben regisztrált eszközök annyi egyidejűleg csatlakoztatott eszközök azonban akkor is.

Az IoT-központot egy részletes ismertető a sávszélesség-szabályozás viselkedését, lásd a következő blogbejegyzésben [IoT Hub-szabályozás és][lnk-throttle-blog].

> [!IMPORTANT]
> Identitás kapcsolatos műveletek az eszközkezelés és a üzembe helyezési forgatókönyvek futásidejű használatra lettek tervezve. Keresztül támogatja a olvasása vagy frissítése nagyszámú eszköz identitások [importálni és exportálni a feladatokat][lnk-importexport].
> 
> 

## <a name="other-limits"></a>Egyéb korlátozások

Az IoT-központ érvényesíti a más működési korlátai:

| Művelet | Korlát |
| --------- | ----- |
| Fájl feltöltése URI-azonosítók | SAS URI-azonosítók 10000 lehet ki egy tárfiók egy időben. <br/> Eszközönként egyszerre 10 SAS URI lehet használatban. |
| Feladatok<sup>1</sup> | Feladat előzményei őrződnek meg mentése vagy 30 napig <br/> Egyidejű feladatok maximális értéke 1 (ingyenes) és S1, (az S2) 5, 10 (S3). |
| További végpontok | Termékváltozat fizetős hubok 10 további végpontokat is rendelkezhet. Szabad SKU hubok előfordulhat, hogy egy további végpontot. |
| Üzenet útválasztási szabályokat | Termékváltozat fizetős hubok 100 útválasztási szabályokat is rendelkezhet. Előfordulhat, hogy az ingyenes SKU hubok öt útválasztási szabályokat. |
| Üzenetküldési eszközről a felhőbe | Maximális üzenet mérete 256 KB |
| Felhő eszközre üzenetküldési<sup>1</sup> | Maximális méret 64 KB. Függőben levő üzenetek a szállítási maximális érték 50. |
| A közvetlen módszer<sup>1</sup> | Maximális közvetlen módszer hasznos mérete 128 KB. |

<sup>1</sup>a szolgáltatás nem érhető el az IoT-központ alapszintű rétegben. További információkért lásd: [kiválasztása a megfelelő IoT-központ](iot-hub-scaling.md).

> [!NOTE]
> Csatlakozhat egy IoT-központ eszközök maximális számát jelenleg 500 000 értéket. Ha azt szeretné, a korlát növeléséhez, forduljon a [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="latency"></a>Késés
Az IoT-központ nagy hangsúlyt fektet adja meg a kis késleltetésű minden műveletnél. Azonban hálózati körülmények és egyéb előre nem látható tényezők miatt azt a maximális késés nem garantálja. A megoldás tervezésekor a következőket:

* Ne bármely a maximális késleltetés feltételezéseket minden IoT Hub művelet elvégzése.
* Az IoT hub, az eszközök legközelebb Azure-régióban kiépítéséhez.
* Érdemes Azure IoT peremhálózati késésérzékeny műveletek elvégzésére, az eszköz vagy egy átjáró megközelíti az eszközt.

Több IoT Hub-egységek befolyásolja a sávszélesség-szabályozás korábban leírt, de nem nyújtanak semmilyen további késés előnyöket vagy garanciát.
Ha nem várt növekszik művelet késés jelenik meg, forduljon a [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>További lépések
Az IoT Hub fejlesztői útmutató egyéb témaköröket tartalmazza:

* [IoT-központok végpontjai][lnk-devguide-endpoints]
* [Az IoT-központ lekérdezési nyelv eszköz twins, a feladatok és az üzenet-útválasztás][lnk-devguide-query]
* [Az IoT Hub MQTT támogatása][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
