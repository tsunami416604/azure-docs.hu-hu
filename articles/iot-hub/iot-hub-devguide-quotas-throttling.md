---
title: Megismerheti az Azure IoT Hub kvótái és szabályozása |} A Microsoft Docs
description: Fejlesztői útmutató – az IoT Hub és a várt szabályozás működésék kvóták leírása.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/11/2019
ms.openlocfilehash: ea9bea8b314d00db87ad7addacc49a976e0da08e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65550468"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Referencia az IoT Hub kvótái és szabályozása

## <a name="quotas-and-throttling"></a>Kvóták és szabályozás

Minden Azure-előfizetés legfeljebb 50 IoT hubok és ingyenes hub legfeljebb 1 lehet.

Üzembe helyezéskor minden IoT hub bizonyos számú egységet tartalmaz egy adott szinten. A szint és az egységek száma határozza meg a maximális napi kvóta küldhet üzeneteket. Az üzenet mérete alapján számítja ki, a napi kvóta egy ingyenes szintű hubbal 0,5 KB és 4 KB-os minden szint esetében. További információkért lásd: [Azure IoT Hub díjszabás](https://azure.microsoft.com/pricing/details/iot-hub/).

A réteg a sávszélesség-szabályozási korlátok, amelyeket az IoT Hub kényszerít minden művelet azt is meghatározza.

## <a name="operation-throttles"></a>A művelet szabályozások

A művelet szabályozások címtartományok perc után lesznek alkalmazva, és célja, hogy a visszaélések megelőzésére arány korlátozások is. Azok a is szabályozzák [forgalomformálásra](#traffic-shaping).

Az alábbi táblázat a kényszerített szabályozások. Értékek tekintse meg az egyes felé.

| Szabályozás | Ingyenes, S1 és B1 | B2 és S2 esetén | B3 és S3 | 
| -------- | ------- | ------- | ------- |
| [Identitásjegyzék műveletei](#identity-registry-operations-throttle) (létrehozása, beolvasása, listázása, frissítése és törlése) | 1.67/sec/Unit (100/perc/egység) | 1.67/sec/Unit (100/perc/egység) | 83.33/sec/Unit (5000/perc/egység) |
| [Új eszköz kapcsolatok](#device-connections-throttle) (Ez a korlátozás vonatkozik arányát _új kapcsolatok_, nem a kapcsolatok teljes száma) | Nagyobb 100/mp-ben vagy a 12/mp/egység <br/> Ha például két S1 szintű egységek-e 2\*12 = 24 új kapcsolatok/mp-ben, de van legalább 100 új kapcsolatok/mp között a egységeket. Kilenc S1-egység, rendelkező 108 új kapcsolatok/mp (9\*12) között a egységeket. | kapcsolatok/mp/egység 120 új | kapcsolatok/mp/egység 6000 új |
| Az eszközről a felhőbe irányuló küldések | Nagyobb 100/mp-ben vagy a 12/mp/egység <br/> Például két S1 szintű egységek a következők: 2\*12 = 24/mp-ben, de van legalább 100/mp között a egységeket. Kilenc S1-egység, rendelkező 108/mp (9\*12) között a egységeket. | 120/sec/unit | 6000/mp/egység |
| Küldések a felhőből az eszközre<sup>1</sup> | 1.67/sec/Unit (100/perc/egység) | 1.67/sec/Unit (100/perc/egység) | 83.33/sec/Unit (5000/perc/egység) |
| Felhőből az eszközre kap<sup>1</sup> <br/> (csak ha eszköz HTTPS PROTOKOLLT használ)| 16.67/sec/Unit (1000/perc/egység) | 16.67/sec/Unit (1000/perc/egység) | 833.33/sec/Unit (50 000/perc/egység) |
| Fájl feltöltése | 1.67 fájl fájlfeltöltési értesítés/mp/egység (100/perc/egység) | 1.67 fájl fájlfeltöltési értesítés/mp/egység (100/perc/egység) | 83.33 fájl fájlfeltöltési értesítés/mp/egység (5000/perc/egység) |
| Közvetlen metódusok<sup>1</sup> | 160KB/sec/unit<sup>2</sup> | 480KB/sec/unit<sup>2</sup> | 24MB/mp/egység<sup>2</sup> | 
| Lekérdezések | 20/perc/egység | 20/perc/egység | 1000/perc/egység |
| A páros olvasási (eszköz- és modul)<sup>1</sup> | 100/sec | Nagyobb 100/mp és 10/mp/egység | 500-as/mp/egység |
| Ikereszköz frissítések (eszköz- és modul)<sup>1</sup> | 50/mp | Magasabb 50/mp vagy 5/mp/egység | 250/mp/egység |
| Operatív feladatok<sup>1</sup> <br/> (létrehozás, frissítés, listázás, törlés) | 1.67/sec/Unit (100/perc/egység) | 1.67/sec/Unit (100/perc/egység) | 83.33/sec/Unit (5000/perc/egység) |
| Eszközművelet feladatok<sup>1</sup> <br/> (iker frissítése, a közvetlen metódus meghívása) | 10/mp | Magasabb 10/mp vagy 1/mp/egység | 50/mp/egység |
| Konfigurációk és az edge-telepítések<sup>1</sup> <br/> (létrehozás, frissítés, listázás, törlés) | 0.33/sec/Unit (20/perc/egység) | 0.33/sec/Unit (20/perc/egység) | 0.33/sec/Unit (20/perc/egység) |
| Eszköz stream kezdeményezés sebessége<sup>1</sup> | 5 új adatfolyamok/mp | 5 új adatfolyamok/mp | 5 új adatfolyamok/mp |
| Egyidejűleg csatlakoztatott eszköz adatfolyamok maximális száma<sup>1</sup> | 50 | 50 | 50 |
| Maximális eszköz stream adatátvitel<sup>1</sup> (kötet naponta összesített) | 300 MB | 300 MB | 300 MB |

<sup>1</sup>Ez a funkció nem érhető el az IoT hub az alapszintű díjcsomagban. További információkért lásd: [kiválasztása a megfelelő IoT Hub](iot-hub-scaling.md). <br/><sup>2</sup>szabályozás mérőszám mérete 4 KB-os.

### <a name="traffic-shaping"></a>Forgalomformálásra

Hirtelen forgalom befogadásához az IoT Hub a szabályozás újabb kérelmek korlátozott ideig fogad el. Az első néhány, a kérések feldolgozása azonnal megtörténik. Ha a kérelmek száma továbbra is megsértik a szabályozás, az IoT Hub elindul egy üzenetsorba helyezi el a kérelmeket, és a korlát díj feldolgozása. Ez a hatás nevezzük *forgalomformálásra*. Továbbá az üzenetsor mérete korlátozott. Ha nem szűnik meg a késleltetési megsértése, végül a várólista megtelik, és az IoT Hub elindul, a kérelmek visszautasítása `429 ThrottlingException`. 

Például használja a szimulált eszköz másodpercenként 200 eszköz – felhő üzeneteket küldhet az S1 IoT Hub (amely legfeljebb 100/mp-D2C küld rendelkezik). Az első percet vagy két az üzenetek feldolgozása azonnal megtörténik. Azonban az eszköz továbbra is fennáll, mint a szabályozási korlát további üzenetek küldéséhez, mivel az IoT Hub csak 100 üzenetek feldolgozásával másodpercenként kezdődik és a többi egy üzenetsorba helyezi. Először okainak nagyobb késéseket. Végül, megkezdődik az `429 ThrottlingException` a várólista azok és a "szabályozási hibák száma" az [az IoT Hub-metrikák](iot-hub-metrics.md) növelése elindul.

### <a name="identity-registry-operations-throttle"></a>Identitásjegyzék műveletei szabályozása

Identitásjegyzék műveletei eszköz eszköz-kezelés és üzembe helyezés forgatókönyvek futásidejű használatra készültek. Olvasása vagy eszközidentitások nagy számú frissítése keresztül támogatott [importálása és exportálása a feladatok](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

### <a name="device-connections-throttle"></a>Eszközkapcsolatok szabályozása

A *eszközkapcsolatok* késleltetési szabályozza a sebesség, amellyel új eszköz kapcsolatok hozhatók létre az IoT hub. A *eszközkapcsolatok* szabályozás nem szabályozza a egyidejűleg csatlakoztatott eszközök maximális számát. A *eszközkapcsolatok* sebesség szabályozása az IoT hub felhasznált egységek számától függ.

Például ha vásárol egy S1-egységet, kap egy késleltetési 100 kapcsolatok száma másodpercenként. Ezért csatlakozhat 100,, 000 eszköz másodpercet vesz igénybe, legalább 1000 (körülbelül 16 perc). Azonban akkor is, hogy regisztrált az eszközidentitás-jegyzékben lévő eszközök egyszerre kapcsolódó eszközről annyi.


## <a name="other-limits"></a>Egyéb korlátok

Az IoT Hub kikényszeríti, egyéb működési korlátai:

| Művelet | Korlát |
| --------- | ----- |
| Eszközök | Csatlakozhat egyetlen IoT hubra eszközök maximális számát 1 000 000. Az egyetlen módszer a kvótahatár növelését, ha kapcsolatba lép [Support](https://azure.microsoft.com/support/options/).| 
| Fájl feltöltése URI-k | 10 000 SAS URI lehet használatban tárfiók esetén egyszerre. <br/> Eszközönként egyszerre 10 SAS URI lehet használatban. |
| Feladatok<sup>1</sup> | Egyidejű feladatok maximális száma (az ingyenes és az S1) 1, 5 (s2 esetén), de 10 (S3 esetén). Azonban az max. egyidejű [eszköz importálási/exportálási feladatok](iot-hub-bulk-identity-mgmt.md) 1 minden szint esetében. <br/>Feladat előzményei megőrződnek mentése 30 nap. |
| További végpontok | A fizetős Termékváltozat hubs lehet 10 további végpontokat. Ingyenes Termékváltozat hubs előfordulhat, hogy egy további végpontot. |
| Üzenet-útválasztási szabályok | A fizetős Termékváltozat hubs 100 útválasztási szabályok rendelkezhetnek. Előfordulhat, hogy az ingyenes Termékváltozat hubs öt útválasztási szabályokat. |
| Eszközről-a-felhőbe üzenetkezelés | Üzenetek maximális mérete 256 KB |
| Üzenetküldés a felhőből az eszközre<sup>1</sup> | Üzenetek maximális mérete 64 KB. Függőben lévő kézbesítésre üzenetek maximális mérete 50. |
| A közvetlen módszer<sup>1</sup> | Maximális közvetlen metódus hasznos adatainak mérete 128 KB-os. |
| Automatikus eszközkonfigurációkat<sup>1</sup> | 100-konfigurációk száma fizetős Termékváltozat hub. 20-konfigurációk száma ingyenes Termékváltozat hub. |
| Automatikus Edge-telepítések<sup>1</sup> | 20 modulok száma üzemelő példányonként. az üzemelő példányok 100 / fizetős Termékváltozat hub. ingyenes Termékváltozat hub száma 20 telepítések. |
| A párok<sup>1</sup> | Ikereszköz szakaszban (címkék, kívánt tulajdonságok, jelentett Tulajdonságok) naplónkénti méretkorlát 8 KB-os |

<sup>1</sup>Ez a funkció nem érhető el az IoT hub az alapszintű díjcsomagban. További információkért lásd: [kiválasztása a megfelelő IoT Hub](iot-hub-scaling.md).

## <a name="increasing-the-quota-or-throttle-limit"></a>Növelje a kvótát, vagy a szabályozási korlát

Egy adott időpontban kvóták növelése vagy sávszélesség-szabályozási korlátai szerint [az IoT hubban kiosztott egységek számának növelésével](iot-hub-upgrade.md).

## <a name="latency"></a>Késés

Az IoT Hub nagy hangsúlyt fektet a alacsony késést tudjon biztosítani a művelet. Azonban hálózati körülmények és más előre nem látható tényezők miatt, bizonyos várakozási ideje nem tudja garantálni. A megoldás tervezésekor a következőket kell tennie:

* Elkerülheti a maximális várakozási feltételezésekre minden IoT Hub művelet elvégzése.
* Kiépítése az IoT hub az eszközök legközelebb áll az Azure-régióban.
* Fontolja meg az Azure IoT Edge késleltetésre érzékeny műveleteket az eszközön, vagy egy átjárón közel az eszköz használatát.

Több IoT Hub-egységek hatással a szabályozás az előzőekben leírtak szerint, de nem ad meg, minden további késéshez előnyöket vagy garanciákat.

Ha váratlan növekedéseit. műveleti késés lát, forduljon a [Support](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>További lépések

Az IoT Hub veszik górcső alá a szabályozás jellege, tekintse meg a következő blogbejegyzésben: [szabályozása az IoT Hub és](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/).

Ez az IoT Hub fejlesztői útmutató más referencia témaköröket tartalmazza:

* [IoT Hub-végpontok](iot-hub-devguide-endpoints.md)
