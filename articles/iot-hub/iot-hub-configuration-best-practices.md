---
title: Az Azure IoT Hub eszközkonfigurációs gyakorlati tanácsai | Microsoft dokumentumok
description: Ismerje meg az automatikus eszközkezelés használatával kapcsolatos gyakorlati tanácsokat az IoT-eszközök nagy méretekben történő kezelésével kapcsolatos ismétlődő és összetett feladatok minimalizálása érdekében.
author: robinsh
ms.author: robinsh
ms.date: 06/28/2019
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 67f0d9eb1fdac603ee82d568644e8ad8550d1c80
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024778"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Az IoT-megoldáson belüli eszközkonfigurációval kapcsolatos gyakorlati tanácsok

Az Azure IoT Hub automatikus eszközkezelése számos ismétlődő és összetett feladatot automatizál a nagy eszközflották teljes életciklusa során. Ez a cikk az IoT-megoldás fejlesztésével és működtetésével kapcsolatos különböző szerepkörök számos ajánlott eljárását határozza meg.

* **IoT hardver gyártó/integrátor:** IoT-hardvergyártók, különböző gyártók tól származó hardvereket összeszerelő integrátorok, vagy más beszállítók által gyártott vagy integrált IoT-telepítéshez hardvert biztosító beszállítók. Részt vesz a firmware, a beágyazott operációs rendszerek és a beágyazott szoftverek fejlesztésében és integrálásában.

* **IoT-megoldás fejlesztője:** Az IoT-megoldás fejlesztését általában egy megoldásfejlesztő végzi. Ez a fejlesztő lehet egy házon belüli csapat vagy egy erre a tevékenységre szakosodott rendszerintegrátor. Az IoT-megoldás fejlesztője az IoT-megoldás különböző összetevőit a semmiből fejlesztheti ki, integrálhat különböző szabványos vagy nyílt forráskódú összetevőket, vagy testre szabhatja az [IoT-megoldásgyorsítót.](/azure/iot-accelerators/)

* **IoT-megoldás operátor:** Az IoT-megoldás üzembe helyezése után hosszú távú műveleteket, figyelést, frissítéseket és karbantartást igényel. Ezeket a feladatokat egy házon belüli csapat végezheti el, amely informatikai szakemberekből, hardverüzemeltetési és karbantartási csapatokból, valamint a teljes IoT-infrastruktúra megfelelő viselkedését figyelő tartományszakértőkből áll.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Az IoT-eszközök nagy méretekben történő konfigurálásához szükséges automatikus eszközkezelés ismertetése

Az automatikus eszközkezelés magában foglalja az [eszköz ikrek](iot-hub-devguide-device-twins.md) és a [modul ikrek](iot-hub-devguide-module-twins.md) számos előnyét a kívánt és a jelentett állapotok szinkronizálásához a felhő és az eszközök között. [Az automatikus eszközkonfigurációk](iot-hub-auto-device-config.md) automatikusan frissítik az ikercsoportok nagy készleteit, és összegezik a haladást és a megfelelőséget. Az alábbi magas szintű lépések az automatikus eszközkezelés kifejlesztése és használata:

* Az **IoT hardver gyártó/integrátor** eszközfelügyeleti funkciókat valósít meg egy beágyazott alkalmazáson belül [az ikereszközök](iot-hub-devguide-device-twins.md)használatával. Ezek a funkciók magukban foglalhatják a belső vezérlőprogram frissítéseit, a szoftvertelepítést és -frissítést, valamint a beállítások kezelését.

* Az **IoT-megoldás fejlesztője** eszköztwins [és](iot-hub-devguide-device-twins.md) automatikus eszközkonfigurációk használatával valósítja meg az eszközkezelési műveletek felügyeleti [rétegét.](iot-hub-auto-device-config.md) A megoldásnak tartalmaznia kell egy kezelői felület meghatározását az eszközkezelési feladatok elvégzéséhez.

* Az **IoT-megoldás operátora** az IoT-megoldás segítségével hajtja végre az eszközfelügyeleti feladatokat, különösen az eszközök csoportosításához, konfigurációs módosítások , például belső vezérlőprogram-frissítések kezdeményezéséhez, a folyamat figyeléséhez és a felmerülő problémák elhárításához.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT hardver gyártó/integrátor

A következő gyakorlati tanácsok a beágyazott szoftverfejlesztéssel foglalkozó hardvergyártók és integrátorok számára:

* **Eszköz [ikrek](iot-hub-devguide-device-twins.md)megvalósítása:** Az eszköztwins engedélyezi a kívánt konfiguráció szinkronizálását a felhőből, valamint az aktuális konfiguráció és az eszköz tulajdonságainak jelentéséhez. A beágyazott alkalmazásokon belüli eszköztwins implementzi a legjobb módja az [Azure IoT SDK-kon](https://github.com/Azure/azure-iot-sdks)keresztül. Az ikereszközök a legalkalmasabbak a konfigurációra, mivel:

    * Támogatja a kétirányú kommunikációt.
    * Lehetővé teszi a csatlakoztatott és a leválasztott eszközállapotokat is.
    * Kövesse a végleges konzisztencia elvét.
    * Teljes mértékben lekérdezhető a felhőben.

* **Az ikereszköz felépítése az eszközkezeléséhez:** Az ikereszköz nek úgy kell felépíteni, hogy az eszközfelügyeleti tulajdonságok logikailag szakaszokba vannak csoportosítva. Ezzel lehetővé teszi a konfigurációs módosítások elkülönítését anélkül, hogy az ikercsoport más szakaszait befolyásolná. Hozzon létre például egy szakaszt a belső vezérlőprogram kívánt tulajdonságaiközött, egy másik szoftverszakaszt, és egy harmadik szakaszt a hálózati beállításokhoz. 

* **Az eszközkezeléshez hasznos eszközattribútumok jelentése:** Az olyan attribútumok, mint a fizikai eszköz gyártmánya és típusa, a belső vezérlőprogram, az operációs rendszer, a sorozatszám és más azonosítók hasznosak a jelentéskészítéshez és a konfigurációs változások célzási paramétereként.

* **Adja meg a jelentési állapot és a folyamat fő állapotait:** A legfelső szintű állapotokat fel kell sorolni, hogy azok jelenthetők legyenek az operátornak. Egy belső vezérlőprogram-frissítés például aktuális, letöltési, alkalmazási, folyamatban lévő és hiba állapotot jelez. További mezők definiálása az egyes állapotokkal kapcsolatos további információkért.

## <a name="iot-solution-developer"></a>IoT-megoldás fejlesztője

Az alábbiakban gyakorlati tanácsok az Azure-ban alapuló IoT-megoldások fejlesztői számára:

* **Eszköz [ikrek](iot-hub-devguide-device-twins.md)megvalósítása:** Az eszköztwins engedélyezi a kívánt konfiguráció szinkronizálását a felhőből, valamint az aktuális konfiguráció és az eszköz tulajdonságainak jelentéséhez. A felhőalapú megoldásokalkalmazásain belüli eszköztwins implementzi a legjobb módja az [Azure IoT SDK-kon](https://github.com/Azure/azure-iot-sdks)keresztül. Az ikereszközök a legalkalmasabbak a konfigurációra, mivel:

    * Támogatja a kétirányú kommunikációt.
    * Lehetővé teszi a csatlakoztatott és a leválasztott eszközállapotokat is.
    * Kövesse a végleges konzisztencia elvét.
    * Teljes mértékben lekérdezhető a felhőben.

* **Eszközök rendszerezése az ikereszközök címkéivel:** A megoldásnak lehetővé kell tennie az üzemeltető számára, hogy minőségi gyűrűket vagy más eszközkészleteket határozzon meg különböző telepítési stratégiák, például kanárik alapján. Az eszközszervezet az eszköz ikercímkék és [lekérdezések](iot-hub-devguide-query-language.md)segítségével valósítható meg a megoldáson belül. Eszközszervezet szükséges, hogy a konfiguráció kiépítése biztonságosan és pontosan.

* ** [Automatikus eszközkonfigurációk megvalósítása:](iot-hub-auto-device-config.md)** Az automatikus eszközkonfigurációk üzembe helyezik és figyelik a konfigurációs változásokat az IOt-eszközök nagy készleteire az ikereszközökön keresztül.

   Automatikus eszközkonfigurációk célkészletek eszköz twins a **célfeltétel,** amely egy lekérdezés tiszaeszköz ikercímkék vagy jelentett tulajdonságok. A **céltartalom** a kívánt tulajdonságok készlete, amely a megcélzott eszköz twins belül lesz beállítva. A céltartalomnak igazodnia kell az IoT hardver gyártója/integrátora által meghatározott ikereszköz-struktúrához. A **metrikák** az ikereszköz jelentett tulajdonságainak lekérdezései, és az IoT-hardver gyártója/integrátor által meghatározott ikereszköz-struktúrához is igazodniuk kell.

   Az automatikus eszközkonfigurációk először röviddel a konfiguráció létrehozása után, majd ötperces időközönként futnak. Ők is kihasználják az IoT Hub két eszköz műveletek olyan sebességgel, amely soha nem lépi túl a [szabályozás korlátot](iot-hub-devguide-quotas-throttling.md) az eszköz iker olvasások és frissítések.

* **Az [eszközkiépítési szolgáltatás](../iot-dps/how-to-manage-enrollments.md)használata:** A megoldásfejlesztőknek az eszközkiépítési szolgáltatás segítségével kell hozzárendelniük az eszköz ikercímkéit az új eszközökhöz, hogy azok automatikusan konfigurálhatók legyenek az adott címkével rendelkező **ikerpárokat célzó automatikus eszközkonfigurációk** által. 

## <a name="iot-solution-operator"></a>IoT-megoldás operátor

Az alábbiakban ajánlott eljárásokat kínál az Azure-ra épülő IoT-megoldásokat használó IoT-megoldás-operátorok számára:

* **Eszközök rendszerezése a felügyelethez:** Az IoT-megoldásnak meg kell határoznia vagy lehetővé kell tennie minőségi gyűrűk vagy más eszközkészletek létrehozását különböző telepítési stratégiák, például kanári kondukkul. Az eszközkészletek a konfigurációs módosítások bevezetésére és más, nagy léptékű eszközkezelési műveletek végrehajtására szolgálnak.

* **Konfigurációs módosítások végrehajtása szakaszos bevezetéssel:**  A fokozatos bevezetés egy olyan átfogó folyamat, amelynek során az operátor az IoT-eszközök szélesítési készletének módosításait telepíti. A cél az, hogy fokozatosan változtatásokat, hogy csökkentsék a kockázatát, hogy széles körű törés változásokat.Az operátornak a megoldás felületét kell használnia egy [automatikus eszközkonfiguráció](iot-hub-auto-device-config.md) létrehozásához, és a célzási feltételnek meg kell céloznia az eszközök kezdeti készletét (például egy kanáricsoportot). Az operátornak ezután ellenőriznie kell a konfigurációs változást az eszközök kezdeti készletében.

   Az ellenőrzés befejezése után az üzemeltető frissíti az automatikus eszközkonfigurációt, hogy nagyobb eszközkészletet tartalmazzon. Az operátornak azt is be kell állítania, hogy a konfiguráció prioritása magasabb legyen, mint az ezen eszközökre jelenleg megcélzott többi konfiguráció. A bevezetés az automatikus eszközkonfiguráció által jelentett metrikák használatával figyelhető.

* **Visszaállítások végrehajtása hibák vagy helytelen konfigurációk esetén:**  A hibákat vagy helytelen konfigurációkat okozó automatikus eszközkonfiguráció visszaállítható a **célzási feltétel** módosításával, hogy az eszközök már nem felelnek meg a célzási feltételnek. Győződjön meg arról, hogy egy másik, alacsonyabb prioritású automatikus eszközkonfiguráció továbbra is az ilyen eszközökre irányul. Ellenőrizze, hogy a visszaállítás sikerült-e a metrikák megtekintésével: A visszagörgetett konfiguráció nak már nem kell megjelenítenie a nem célzott eszközök állapotát, és a második konfiguráció metrikáknak most antól tartalmazniuk kell a továbbra is megcélzott eszközök számát.

## <a name="next-steps"></a>További lépések

* Ismerje meg az eszközök ikreinek megvalósítását [az IOt Hubban az eszközök ikreinek megértése és használata során.](iot-hub-devguide-device-twins.md)

* Az [IoT-eszközök nagy méretekben történő konfigurálása és figyelése](iot-hub-auto-device-config.md)című fázisban bemutatja az automatikus eszközkonfiguráció létrehozásának, frissítésének és törlésének lépéseit.

* Belső vezérlőprogram-frissítési minta megvalósítása az eszköztwins és az automatikus eszközkonfigurációk használatával [az oktatóanyagban: Eszköz belső vezérlőprogramjának frissítési folyamatának megvalósítása](tutorial-firmware-update.md).
