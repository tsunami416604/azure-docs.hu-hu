---
title: Az Azure IoT Hub-kvóták és szabályozás ismertetése | Microsoft dokumentumok
description: Fejlesztői útmutató – az IoT Hubra vonatkozó kvóták leírása és a várható szabályozási viselkedés.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 71a5737434e78bc39bccdfeb950e0dbc32ed0052
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284693"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Referencia – IoT Hub-kvóták és szabályozás

Ez a cikk ismerteti az IoT Hub kvótáit, és információkat tartalmaz a szabályozás működésének megértéséhez.

## <a name="quotas-and-throttling"></a>Kvóták és szabályozás

Minden Azure-előfizetés legbénult 50 IoT-központtal rendelkezhet, és legbénákban 1 ingyenes központtal.

Üzembe helyezéskor minden IoT hub bizonyos számú egységet tartalmaz egy adott szinten. Az egységek szintje és száma határozza meg az elküldhető üzenetek maximális napi kvótáját. A napi kvóta kiszámításához használt üzenetmérete0,5 KB egy ingyenes rétegű hub és 4 KB az összes többi réteg esetében. További információ: [Azure IoT Hub díjszabása.](https://azure.microsoft.com/pricing/details/iot-hub/)

A szint is meghatározza a szabályozáskorlátokat, amelyek az IoT Hub minden műveletben kényszeríti.

### <a name="iot-plug-and-play"></a>IoT Plug and Play

A nyilvános előzetes verzió során az IoT Plug and Play eszközök felületenként külön üzeneteket küldenek, ami növelheti az üzenetkvótába beszámított üzenetek számát.

## <a name="operation-throttles"></a>Fojtószelepek működtetése

A műveletszabályozás olyan sebességkorlátozások, amelyek perctartományokban vannak alkalmazva, és a visszaélések megelőzésére szolgálnak. Ők is ki vannak téve a [forgalom alakításában](#traffic-shaping).

Az alábbi táblázat az kényszerített szabályozásokat mutatja be. Az értékek egy adott hubra vonatkoznak.

| Szabályozás | Ingyenes, B1 és S1 | B2 és S2 | B3 és S3 | 
| -------- | ------- | ------- | ------- |
| [Identitás-beállításjegyzék-műveletek](#identity-registry-operations-throttle) (létrehozás, beolvasás, lista, frissítés, törlés) | 1,67/mp/egység (100/perc/egység) | 1,67/mp/egység (100/perc/egység) | 83,33/mp/egység (5000/perc/egység) |
| [Új eszközkapcsolatok](#device-connections-throttle) (ez a korlát az _új kapcsolatok_sebességére vonatkozik, nem a kapcsolatok teljes számára) | Magasabb 100/sec vagy 12/sec/egység <br/> Például két S1 egység\*2 12 = 24 új kapcsolat/mp, de legalább 100 új kapcsolat/mp van az egységek között. Kilenc S1 egységgel 108 új kapcsolat/mp\*(9 12) van az egységek között. | 120 új csatlakozás/mp/egység | 6000 új kapcsolat/mp/egység |
| Az eszközről a felhőbe irányuló küldések | Magasabb, mint 100 küldési művelet/mp vagy 12 küldési művelet/mp/egység <br/> Például két S1 egység\*2 12 = 24/mp, de legalább 100 küldési művelet/mp van az egységek között. Kilenc S1 egységgel 108 küldési művelettel/mp -vel (9\*12) rendelkezik az egységek között. | 120 küldési művelet/mp/egység | 6000 küldési művelet/mp/egység |
| Felhőből az eszközre küld<sup>1</sup> | 1.67 küldési műveletek/mp/egység (100 üzenet/perc/egység) | 1,67 küldési művelet/mp/egység (100 küldési művelet/perc/egység) | 83.33 küldési műveletek/mp/egység (5000 küldési művelet/perc/egység) |
| A felhőből az<sup>1</sup> eszközre 1 <br/> (csak akkor, ha az eszköz HTTPS-t használ)| 16.67 vételi műveletek/mp/egység (1000 fogadási művelet/perc/egység) | 16.67 vételi műveletek/mp/egység (1000 fogadási művelet/perc/egység) | 833.33 fogadási műveletek/mp/egység (50 000 fogadási művelet/perc/egység) |
| Fájl feltöltése | 1,67 fájl feltöltési kezdeményezés/mp/egység (100/perc/egység) | 1,67 fájl feltöltési kezdeményezés/mp/egység (100/perc/egység) | 83.33 fájl feltöltési kezdeményezések/mp/egység (5000/perc/egység) |
| Közvetlen módszerek<sup>1</sup> | 160 KB/mp/egység<sup>2</sup> | 480 KB/mp/egység<sup>2</sup> | 24 MB/mp/egység<sup>2</sup> | 
| Lekérdezések | 20/min/egység | 20/min/egység | 1000/min/egység |
| Twin (eszköz és modul) olvasás<sup>1</sup> | 100/mp | Magasabb 100/sec vagy 10/sec/egység | 500/mp/egység |
| Ikerfrissítések (eszköz és modul)<sup>1</sup> | 50/mp | Magasabb 50/sec vagy 5/sec/egység | 250/mp/egység |
| Feladatműveletek<sup>1</sup> <br/> (létrehozás, frissítés, listázás, törlés) | 1,67/mp/egység (100/perc/egység) | 1,67/mp/egység (100/perc/egység) | 83,33/mp/egység (5000/perc/egység) |
| Feladatok eszközműveletek<sup>1</sup> <br/> (ikerfrissítés, közvetlen metódus meghívása) | 10/mp | Magasabb 10/sec vagy 1/sec/egység | 50/mp/egység |
| Konfigurációk és peremhálózati telepítések<sup>1</sup> <br/> (létrehozás, frissítés, listázás, törlés) | 0,33/mp/egység (20/perc/egység) | 0,33/mp/egység (20/perc/egység) | 0,33/mp/egység (20/perc/egység) |
| Eszközfolyam-kezdeményezési sebesség<sup>1</sup> | 5 új adatfolyam/mp | 5 új adatfolyam/mp | 5 új adatfolyam/mp |
| Az egyidejűleg csatlakoztatott eszközadatfolyamok maximális száma<sup>1</sup> | 50 | 50 | 50 |
| Maximális eszközadat-továbbítás<sup>1</sup> (összesített mennyiség naponta) | 300 MB | 300 MB | 300 MB |

<sup>1 1</sup> Ez a funkció nem érhető el az IoT Hub alapszintjén. További információ: [Hogyan választhatja ki a megfelelő IoT Hubot.](iot-hub-scaling.md) <br/><sup>2.</sup> A fojtásmérő mérete 4 KB.

### <a name="throttling-details"></a>Szabályozás részletei

* A mérő mérete határozza meg, hogy milyen lépésekben a sávszélesség-szabályozási korlát fogyasztják. Ha a közvetlen hívás hasznos adatai 0 és 4 KB között vannak, akkor az 4 KB-nak számít. A 160 KB/mp/egység korlát beütése előtt másodpercenként legfeljebb 40 hívást kezdeményezhet.

   Hasonlóképpen, ha a hasznos adat 4 KB és 8 KB között van, minden hívás 8 KB-ot számlál, és egységenként legfeljebb 20 hívást tehet le, mielőtt elérne a maximális korlátot.

   Végül, ha a hasznos teher mérete 156 KB és 160 KB között van, akkor a 160 KB/mp/egység korlát betöltése előtt egyszerre csak másodpercenként 1 hívást kezdeményezhet a hubon.

*  *Jobs eszközműveletek (update twin, közvetlen metódus meghívása)* a tier S2, 50/sec/unit csak akkor vonatkozik, ha metódusok használata feladatokat. Ha közvetlen metódusokat közvetlenül hív meg, az eredeti 24 MB/mp/mp/egység (S2) szabályozási korlát érvényes.

*  **Kvóta** a központba *naponta*küldhető üzenetek összesített száma. A központ kvótakorlátját az **Üzenetek/nap összesen számozási** lapján, az [IoT Hub díjszabási lapján](https://azure.microsoft.com/pricing/details/iot-hub/)találja.

*  A felhőből az eszközre és az eszközről a felhőbe irányuló szabályozás határozza meg az üzenetek küldésének maximális *sebességét* – az üzenetek számát, 4 KB-os adattömböktől függetlenül. Minden üzenet legfeljebb 256 KB lehet, ami a [maximális üzenetméret](iot-hub-devguide-quotas-throttling.md#other-limits).

*  Ez egy jó gyakorlat, hogy a hívások szabályozása, hogy ne nyomja meg/ meghaladja a sávszélesség-szabályozási korlátokat. Ha nem éri el a korlátot, az IoT Hub 429-es hibakóddal válaszol, és az ügyfélnek vissza kell állnia, és újra meg kell próbálnia. Ezek a korlátok hubonként (vagy bizonyos esetekben hub/egységenként) vannak. További információt a [Kapcsolat és a megbízható üzenetküldés/újrapróbálkozási minták kezelése](iot-hub-reliability-features-in-sdks.md#retry-patterns)című dokumentumban talál.

### <a name="traffic-shaping"></a>Forgalomalakítás

A burst forgalom befogadására az IoT Hub korlátozott ideig fogadja a fojtószelep feletti kérelmeket. A kérelmek közül az első néhány at azonnal feldolgozzuk. Ha azonban a kérelmek száma továbbra is megsérti a szabályozást, az IoT Hub megkezdi a kérelmek várólistába helyezését, és a korlátarányban dolgozza fel. Ezt a hatást *forgalomalakításnak nevezzük.* Továbbá a várólista mérete korlátozott. Ha a szabályozás megsértése folytatódik, végül a várólista megtelik, és `429 ThrottlingException`az IoT Hub elkezdi a kérelmek elutasítását.

Például egy szimulált eszköz használatával másodpercenként 200 eszközről felhőbe irányuló üzenetet küldhet az S1 IoT Hubnak (amelynek legfeljebb 100/mp D2C-s küldése van). Az első vagy két percben az üzenetek feldolgozása azonnal megtörtén. Mivel azonban az eszköz továbbra is több üzenetet küld, mint a sávszélesség-szabályozási korlát, az IoT Hub másodpercenként csak 100 üzenetet dolgoz fel, és a többit várólistába helyezi. Elkezdi észrevenni a megnövekedett késleltetést. Végül elkezdi `429 ThrottlingException` bekapni, ahogy a várólista megtelik, és az [IoT Hub metrikáiban](iot-hub-metrics.md) a "szabályozáshibák száma" növekszik.

### <a name="identity-registry-operations-throttle"></a>Identitás-beállításjegyzék-műveletek szabályozása

Az eszközidentitás-beállításjegyzék-műveletek az eszközkezelési és kiépítési forgatókönyvekben való futásidejű használatra szolgálnak. Nagyszámú eszközidentitás olvasása vagy frissítése az [importálási és exportálási feladatok](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)révén támogatott.

### <a name="device-connections-throttle"></a>Eszközkapcsolatok szabályozása

Az *eszközkapcsolatok* szabályozása szabályozza, hogy milyen sebességgel lehet új eszközkapcsolatokat létrehozni egy IoT hubbal. Az *eszközkapcsolatok* szabályozása nem szabályozza az egyidejűleg csatlakoztatott eszközök maximális számát. Az *eszközkapcsolatok* sebessége szabályozása az IoT hubhoz kiépített egységek számától függ.

Ha például egyetlen S1 egységet vásárol, másodpercenként 100 kapcsolatból álló fojtószelepet kap. Ezért 100 000 eszköz csatlakoztatásához legalább 1000 másodpercet (körülbelül 16 percet) vesz igénybe. Azonban annyi egyidejűleg csatlakoztatott eszközt használhat, ahány eszköz regisztrálva van a személyazonosság-beállításjegyzékében.

## <a name="other-limits"></a>Egyéb korlátok

Az IoT Hub más működési korlátokat is érvényesít:

| Művelet | Korlát |
| --------- | ----- |
| Eszközök | Az egyetlen IoT-hubra regisztrálható eszközök és modulok teljes száma 1 000 000. A korlát növelésének egyetlen módja, ha kapcsolatba lép a [Microsoft támogatási szolgálatával.](https://azure.microsoft.com/support/options/)|
| Fájlfeltöltések | Eszközönként 10 egyidejű fájlfeltöltés. |
| Állások<sup>1</sup> | A maximális egyidejű feladatok: 1 (Ingyenes és S1), 5 (S2 esetén) és 10 (S3 esetén). A maximális egyidejű [eszközimportálási/-exportálási feladatok](iot-hub-bulk-identity-mgmt.md) azonban 1 az összes szintre. <br/>A feladatelőzmények legfeljebb 30 napig maradnak meg. |
| További végpontok | A fizetős Termékváltozat-központok 10 további végponttal rendelkezhetnek. Az ingyenes termékváltozat-központok nak egy további végpontja is lehet. |
| Üzenet-útválasztási lekérdezések | A fizetős Termékváltozat-központok 100 útválasztási lekérdezést tartalmazhatnak. Az ingyenes termékváltozat-központok nak öt útválasztási lekérdezése lehet. |
| Üzenetbővítések | A fizetős Termékváltozat-központok legfeljebb 10 üzenetbővítést kaphatnak. Az ingyenes Termékváltozat-központok legfeljebb 2 üzenetbővítést tehetnek fel.|
| Eszközről felhőbe irányuló üzenetküldés | Az üzenetek maximális mérete 256 KB |
| Felhőből eszközre történő üzenetküldés<sup>1</sup> | Maximális üzenetméret 64 KB. A kézbesítésre váró függőben lévő üzenetek maximális ideje eszközönként 50. |
| <sup>1.</sup> közvetlen módszer | A közvetlen metódus maximális hasznos adatmérete 128 KB. |
| Automatikus eszköz- és modulkonfigurációk<sup>1</sup> | 100 konfiguráció fizetős Termékváltozat-központonként. 20 konfiguráció ingyenes Termékváltozat-központonként. |
| IoT Edge automatikus telepítések<sup>1</sup> | 50 modul telepítésenként. 100 központi telepítés (beleértve a réteges központi telepítéseket) fizetős Termékváltozat-központonként. 10 központi telepítés ingyenes Termékváltozat-központonként. |
| Ikrek<sup>1</sup> | A kívánt tulajdonságok és a jelentett tulajdonságok maximális mérete egyenként 32 KB. A címkék maximális mérete 8 KB. |
| Megosztott elérési házirendek | A megosztott hozzáférési házirendek maximális száma 16 |

<sup>1 1</sup> Ez a funkció nem érhető el az IoT Hub alapszintjén. További információ: [Hogyan választhatja ki a megfelelő IoT Hubot.](iot-hub-scaling.md)

## <a name="increasing-the-quota-or-throttle-limit"></a>A kvóta- vagy szabályozási korlát növelése

Bármikor növelheti a kvóták vagy a sávszélesség-szabályozási korlátok at [az IoT hubban kiosztott egységek számának növelésével.](iot-hub-upgrade.md)

## <a name="latency"></a>Késés

Az IoT Hub arra törekszik, hogy az összes művelet alacsony késést biztosítson. Azonban a hálózati feltételek és más előre nem látható tényezők miatt nem tudja garantálni a bizonyos késést. A megoldás tervezésekor a következőket kell tennie:

* Ne feltételezz semmit az IoT Hub-műveletek maximális késéséről.
* Az IOt-központ kiépítése az Azure-régióban legközelebb az eszközökhöz.
* Fontolja meg az Azure IoT Edge használatával késés-érzékeny műveletek végrehajtásához az eszközön vagy az eszköz höz közeli átjárón.

Több IoT Hub-egységek hatással van a szabályozás, a korábban leírt módon, de nem nyújtanak további késési előnyöket vagy garanciákat.

Ha a működési késés nem várt növekedését látja, forduljon [a Microsoft támogatási szolgálatához.](https://azure.microsoft.com/support/options/)

## <a name="next-steps"></a>További lépések

Az IoT Hub szabályozási viselkedésének részletes vitafórumát az [IoT Hub-szabályozás és a (Előfordulhat, hogy a](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)szolgáltatás ban) című blogbejegyzésben talál részletesen.

Az IoT Hub fejlesztői útmutatójának további referenciatémakörei a következők:

* [IoT Hub-végpontok](iot-hub-devguide-endpoints.md)
