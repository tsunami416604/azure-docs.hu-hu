---
title: "Használja az Azure IoT peremeszközök átjáróként |} Microsoft Docs"
description: "A Azure IoT Edge böngésző számára hozzon létre egy átlátszó nem átlátszó vagy a proxy átjáróeszköz, amely adatokat küld a több alárendelt eszközről a felhőbe, vagy helyileg feldolgozza azt."
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/27/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: fe7ad2444b9378550e9624e3d109c8be4fd29f23
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway---preview"></a>Hogyan IoT peremhálózati eszköz egy átjáró - használható megtekintése

Az IoT-megoldások átjárói célja a megoldáshoz, és eszközkapcsolatok egyesíthető biztonsági elemzés. Az Azure IoT peremhálózati függetlenül attól, hogy áll(nak) kapcsolat, identity vagy peremhálózati analytics-átjáró IoT-szükségletét használható. Ez a cikk átjáró minták csak tekintse meg jellemzői alárendelt eszközkapcsolatok és eszközidentitás, nem hogyan eszközön tárolt adatok feldolgozása az átjárón.

## <a name="patterns"></a>Minták
Nincsenek átjáróként IoT peremhálózati eszköz használata három mintái: átlátszó, protokoll, fordítási és identitás fordítási:
* **Transzparens** – elméletileg csatlakozott az IoT Hub eszközöket ehelyett csatlakozhat egy átjáróeszköz. Ez azt jelenti, hogy az alsóbb rétegbeli eszközök saját IoT-központ identitással rendelkezik, és a MQTT, AMQP vagy HTTP-protokollokat használ. Az átjáró egyszerűen továbbítja a kommunikációt az eszközök és az IoT-központ között. Az eszközök nem tudnak, hogy a felhő egy átjárón keresztül kommunikálnak, és a felhasználó az eszközök az IoT hubon való interakció nem érzékeli a köztes átjáróeszközt. Így az átjáró nem átlátszó. Tekintse meg a [átlátszó átjáró létrehozása] [ lnk-iot-edge-as-transparent-gateway] – útmutató a részletekért átlátszó átjáróként IoT peremhálózati eszköz használatával.
* **Fordítási protokoll** – eszközök, amelyek nem támogatják az MQTT, AMQP vagy HTTP átjáróeszközzel segítségével adatokat küldeni az IoT-központ. Az átjáró az intelligens ahhoz, hogy az eszközöket; által használt protokoll ismertetése azonban az csak eszközt, az IoT hubon identitása. Minden információt tűnik, hogy egy eszköz, az átjáró érkezik. Ez azt jelenti, hogy eszközöket az üzenetek kell beágyazása további információkra, ha a felhőalapú alkalmazásokhoz szeretné, hogy az adatok körül forog eszközönkénti alapon OK. Továbbá az IoT-központ primitívek, például a két és metódusok csak az átjáró eszköz, nem eszközöket.
* **Identitás fordítási** -eszközöket, amelyek nem kapcsolódnak az IoT-központ csatlakozni egy átjáróeszköz, amely az IoT-központ biztosít az identitás- és protokoll fordítási az alsóbb rétegbeli eszközök nevében. Az átjáró az intelligens ahhoz ismerje meg az alsóbb rétegbeli eszközök által használt protokoll, -azonosítót fog kérni, és az IoT-központ primitívek lefordítani. Eszközöket első osztályú eszközökön twins és módszerek az IoT-központ jelennek meg. A felhasználó és az IoT-központ az eszközök kommunikációját, és nem érzékeli a köztes átjáróeszközt.

![Annak a átjáró diagramok][1]

## <a name="use-cases"></a>Használati esetek
Minden átjáró mintát a következő előnyöket biztosítják:
* **A szegély analytics** – használható AI szolgáltatások helyileg történő elküldése nélkül eszközöket érkező adatok feldolgozása teljes hűség telemetriai adatokat a felhőbe. Található és helyileg insights reagálnak, és csak az adatok egy részét küldeni az IoT-központ. 
* **Az alárendelt eszköz elkülönítési** – az átjáró eszköz védelme is biztosítható, hogy az interneten való kitettség összes eszközöket. Azt a helyet egy soha hálózati kapcsolattal nem rendelkező és amely a webes hozzáférést biztosít az IT-hálózat Between kaphat például. 
* **Kapcsolat multiplexáló** – minden eszköz csatlakoztatása az IoT-központot egy IoT-Edge révén eszköz ugyanazt az alapul szolgáló kapcsolatot használja.
* **Forgalom simítás** -az IoT-peremhálózati eszköz automatikusan valósítja meg az exponenciális leállítási szabályozás, az üzenetek a helyi megőrzése közben az IoT-központ esetén. Ezáltal a megoldás rugalmas a forgalmat a teljesítményt.
* **Korlátozott offline támogatást** – az átjáró eszköz helyileg üzeneteket fogja tárolni, és a két olyan frissítés, amelyet nem lehet kézbesíteni az IoT-központ.

Egy átjáró biztosítja protokollfordításhoz is elvégezheti, biztonsági elemzés, a eszköz elkülönítési, a forgalom simítás és a meglévő és új eszközre, amelyek korlátozott erőforrás offline támogatást. Több meglévő eszközt olyan adatokat is kiemelt üzleti elemzéseket; azonban ezek nem képesek a szem előtt a felhő kapcsolatban. Nem átlátszó átjárók engedélyezik a oldhatja fel és egy teljes körű IoT-megoldás által használt adatok.

Egy átjáró, amely identitás-fordítási előnyei a protokollfordításhoz, és emellett lehetővé teszik a teljes kezelhetőségével eszközöket a felhőből. Beszéd összes eszközt az IoT megoldás megjelenjenek az IoT-központ azokat a protokolltól függetlenül.

## <a name="cheat-sheet"></a>Adatlap
Íme egy gyors csal lap, amely összehasonlítja az IoT-központ primitívek átlátszó, nem átlátszó használatakor, és proxy átjárók.

| &nbsp; | Transzparens átjáró | Protokollfordításhoz | Identitás fordítás |
|--------|-------------|--------|--------|
| Az IoT-központ identitásjegyzékhez tárolt identitások | A minden csatlakoztatott eszközön azonosítói | Csak az átjáró eszköz identitása | A minden csatlakoztatott eszközön azonosítói |
| Ikereszközök | Minden csatlakoztatott eszközön van a saját eszköz iker | Csak az átjáró eszköz és a modul twins rendelkezik | Minden csatlakoztatott eszközön van a saját eszköz iker |
| Közvetlen módszerek és a felhő-eszközre küldött üzenetek | A felhő külön-külön lehet oldani minden csatlakoztatott eszközön | A felhő csak lehet oldani az átjáró eszköz | A felhő külön-külön lehet oldani minden csatlakoztatott eszközön |
| [Az IoT-központ szabályozások és kvóták][lnk-iothub-throttles-quotas] | Minden eszköz alkalmazása | Az átjáró eszköz alkalmazása | Minden eszköz alkalmazása |

Ha egy nem átlátszó átjáró (protokollfordításhoz) mintát használ, adott átjárón keresztül csatlakozó összes eszközök azonos felhő eszközre beolvasása, amely legfeljebb 50 üzenetek tartalmazhat osztják meg. Ez azt jelenti, hogy az átjáró nem átlátszó mintát kell használni, csak akkor, ha nagyon néhány olyan eszközt minden mező átjárón keresztül kapcsolódik, és a felhő eszközre forgalmukat értéke alacsony.

## <a name="next-steps"></a>További lépések
Az IoT peremhálózati eszköz legyen egy [átlátszó átjáró][lnk-iot-edge-as-transparent-gateway] 

[lnk-iot-edge-as-transparent-gateway]: ./how-to-create-transparent-gateway.md
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md

[1]: ./media/iot-edge-as-gateway/edge-as-gateway.png
