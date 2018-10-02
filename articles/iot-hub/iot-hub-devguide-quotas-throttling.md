---
title: Megismerheti az Azure IoT Hub kvótái és szabályozása |} A Microsoft Docs
description: Fejlesztői útmutató – az IoT Hub és a várt szabályozás működésék kvóták leírása.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: dobett
ms.openlocfilehash: 377f28f4798a1ffb6423c1f4d9f4e34b4fc60234
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48018842"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Referencia az IoT Hub kvótái és szabályozása

## <a name="quotas-and-throttling"></a>Kvóták és szabályozás

Minden Azure-előfizetés legfeljebb 50 IoT hubok és ingyenes hub legfeljebb 1 lehet.

Minden IoT-központ ki van építve a bizonyos számú egység az adott szinten. A szint és az egységek száma határozza meg a maximális napi kvóta küldhet üzeneteket. Az üzenet mérete alapján számítja ki, a napi kvóta egy ingyenes szintű hubbal 0,5 KB és 4 KB-os minden szint esetében. További információkért lásd: [Azure IoT Hub díjszabás](https://azure.microsoft.com/pricing/details/iot-hub/).

A réteg a sávszélesség-szabályozási korlátok, amelyeket az IoT Hub kényszerít minden művelet azt is meghatározza.

## <a name="operation-throttles"></a>A művelet szabályozások

A művelet szabályozások címtartományok perc után lesznek alkalmazva, és célja, hogy a visszaélések megelőzése arány korlátozások is. Az IoT Hub próbál elkerülése érdekében, amikor csak lehetséges hibát adna vissza, de elindítja a visszatérő `429 ThrottlingException` Ha túl sokáig a szabályozás sérül.

Egy adott időpontban növelheti a kvóták vagy szabályozási korlátairól növelje az IoT hubban kiosztott egységek számát.

Az alábbi táblázat a kényszerített szabályozások. Értékek tekintse meg az egyes felé.

| Szabályozás | Ingyenes, S1 és B1 | B2 és S2 esetén | B3 és S3 | 
| -------- | ------- | ------- | ------- |
| Identitásjegyzék műveletei (létrehozása, beolvasása, listázása, frissítése és törlése) | 1.67/sec/Unit (100/perc/egység) | 1.67/sec/Unit (100/perc/egység) | 83.33/sec/Unit (5000/perc/egység) |
| Új eszköz kapcsolatok (Ez a korlátozás vonatkozik gyakorisággal _új kapcsolatok_ létrehozása történik, nem kapcsolatok teljes száma) | Nagyobb 100/mp-ben vagy a 12/mp/egység <br/> Ha például két S1 szintű egységek-e 2\*12 = 24 új kapcsolatok/mp-ben, de van legalább 100 új kapcsolatok/mp között a egységeket. Kilenc S1-egység, rendelkező 108 új kapcsolatok/mp (9\*12) között a egységeket. | kapcsolatok/mp/egység 120 új | 6000 új kapcsolatok/mp/egység |
| Az eszközről a felhőbe irányuló küldések | Nagyobb 100/mp-ben vagy a 12/mp/egység <br/> Például két S1 szintű egységek a következők: 2\*12 = 24/mp-ben, de van legalább 100/mp között a egységeket. Kilenc S1-egység, rendelkező 108/mp (9\*12) között a egységeket. | 120/mp/egység | 6000/mp/egység |
| Küldések a felhőből az eszközre<sup>1</sup> | 1.67/sec/Unit (100/perc/egység) | 1.67/sec/Unit (100/perc/egység) | 83.33/sec/Unit (5000/perc/egység) |
| Felhőből az eszközre kap<sup>1</sup> <br/> (csak ha eszköz HTTPS PROTOKOLLT használ)| 16.67/sec/Unit (1000/perc/egység) | 16.67/sec/Unit (1000/perc/egység) | 833.33/sec/Unit (50000/perc/egység) |
| Fájl feltöltése | 1.67 fájl fájlfeltöltési értesítés/mp/egység (100/perc/egység) | 1.67 fájl fájlfeltöltési értesítés/mp/egység (100/perc/egység) | 83.33 fájl fájlfeltöltési értesítés/mp/egység (5000/perc/egység) |
| Közvetlen metódusok<sup>1</sup> | 160KB/sec/unit<sup>2</sup> | 480KB/sec/unit<sup>2</sup> | 24MB/mp/egység<sup>2</sup> | 
| A páros olvasási (eszköz- és modul)<sup>1</sup> | 10/mp | Magasabb 10/mp vagy 1/mp/egység | 50/mp/egység |
| Ikereszköz frissítések (eszköz- és modul)<sup>1</sup> | 10/mp | Magasabb 10/mp vagy 1/mp/egység | 50/mp/egység |
| Operatív feladatok<sup>1,3</sup> <br/> (létrehozás, frissítés, listázás, törlés) | 1.67/sec/Unit (100/perc/egység) | 1.67/sec/Unit (100/perc/egység) | 83.33/sec/Unit (5000/perc/egység) |
| Eszközművelet feladatok<sup>1</sup> <br/> (iker frissítése, a közvetlen metódus meghívása) | 10/mp | Magasabb 10/mp vagy 1/mp/egység | 50/mp/egység |
| Konfigurációk és az edge-telepítések<sup>1</sup> <br/> (létrehozás, frissítés, listázás, törlés) | 0.33/sec/Unit (20/perc/egység) | 0.33/sec/Unit (20/perc/egység) | 0.33/sec/Unit (20/perc/egység) |


<sup>1</sup>Ez a funkció nem érhető el az IoT hub az alapszintű díjcsomagban. További információkért lásd: [kiválasztása a megfelelő IoT Hub](iot-hub-scaling.md). <br/><sup>2</sup>mérőszám méret lett 8 KB-os. <br/><sup>3</sup>akkor legfeljebb egy aktív eszköz importálási/exportálási feladat egyszerre.

A *eszközkapcsolatok* késleltetési szabályozza a sebesség, amellyel új eszköz kapcsolatok hozhatók létre az IoT hub. A *eszközkapcsolatok* szabályozás nem szabályozza a egyidejűleg csatlakoztatott eszközök maximális számát. A *eszközkapcsolatok* sebesség szabályozása az IoT hub felhasznált egységek számától függ.

Például ha vásárol egy S1-egységet, kap egy késleltetési 100 kapcsolatok száma másodpercenként. Tehát 100 000 eszközt a csatlakozáshoz másodpercet vesz igénybe legalább 1000 (körülbelül 16 percig). Azonban akkor is, hogy regisztrált az eszközidentitás-jegyzékben lévő eszközök egyszerre kapcsolódó eszközről annyi.

Az IoT Hub veszik górcső alá a szabályozás jellege, tekintse meg a következő blogbejegyzésben: [szabályozása az IoT Hub és](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/).

> [!IMPORTANT]
> Identitásjegyzék műveletei eszköz kezelés és üzembe helyezés forgatókönyvek futásidejű használatra készültek. Olvasása vagy eszközidentitások nagy számú frissítése keresztül támogatott [importálása és exportálása a feladatok](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).
> 
> 

## <a name="other-limits"></a>Egyéb korlátok

Az IoT Hub kikényszeríti, egyéb működési korlátai:

| Művelet | Korlát |
| --------- | ----- |
| Fájl feltöltése URI-k | 10000 SAS URI lehet használatban storage-fiókok egyszerre. <br/> Eszközönként egyszerre 10 SAS URI lehet használatban. |
| Feladatok<sup>1</sup> | Feladat előzményei őrződnek fel, és 30 nap <br/> Egyidejű feladatok maximális száma az 1 (ingyenes) és az S1, 5 (s2 esetén), 10 (S3 esetén). |
| További végpontok | A fizetős Termékváltozat hubs lehet 10 további végpontokat. Ingyenes Termékváltozat hubs előfordulhat, hogy egy további végpontot. |
| Üzenet-útválasztási szabályok | A fizetős Termékváltozat hubs 100 útválasztási szabályok rendelkezhetnek. Előfordulhat, hogy az ingyenes Termékváltozat hubs öt útválasztási szabályokat. |
| Eszközről-a-felhőbe üzenetkezelés | Üzenetek maximális mérete 256 KB |
| Üzenetküldés a felhőből az eszközre<sup>1</sup> | Üzenetek maximális mérete 64 KB. Függőben lévő kézbesítésre üzenetek maximális mérete 50. |
| A közvetlen módszer<sup>1</sup> | Maximális közvetlen metódus hasznos adatainak mérete 128 KB-os. |
| Konfigurációk | 20-konfigurációk száma hub. |
| Edge-telepítések | 20 telepítések száma az eseményközpont. 20 modulok száma üzemelő példányonként. |
| A párok | Ikereszköz szakaszban (címkék, kívánt tulajdonságok, jelentett Tulajdonságok) naplónkénti méretkorlát 8 KB-os |

<sup>1</sup>Ez a funkció nem érhető el az IoT hub az alapszintű díjcsomagban. További információkért lásd: [kiválasztása a megfelelő IoT Hub](iot-hub-scaling.md).

> [!NOTE]
> Jelenleg az egyetlen IoT hubra csatlakozhat eszközök maximális száma 500 000 el. Ha ezt a korlátot növelni szeretné, lépjen kapcsolatba [Support](https://azure.microsoft.com/support/options/).

## <a name="latency"></a>Késés
Az IoT Hub nagy hangsúlyt fektet a alacsony késést tudjon biztosítani a művelet. Azonban hálózati körülmények és más előre nem látható tényezők miatt, a maximális késés nem tudja garantálni. A megoldás tervezésekor a következőket kell tennie:

* Elkerülheti a maximális várakozási feltételezésekre minden IoT Hub művelet elvégzése.
* Kiépítése az IoT hub az eszközök legközelebb áll az Azure-régióban.
* Fontolja meg az Azure IoT Edge késleltetésre érzékeny műveleteket az eszközön, vagy egy átjárón közel az eszköz használatát.

Több IoT Hub-egységek hatással a szabályozás az előzőekben leírtak szerint, de nem ad meg, minden további késéshez előnyöket vagy garanciákat.

Ha váratlan növekedéseit. műveleti késés lát, forduljon a [Support](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>További lépések

Ez az IoT Hub fejlesztői útmutató más referencia témaköröket tartalmazza:

* [IoT Hub-végpontok](iot-hub-devguide-endpoints.md)
