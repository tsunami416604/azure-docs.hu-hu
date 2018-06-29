---
title: Eszköz konfigurációs ajánlott eljárások az Azure IoT Hub |} Microsoft Docs
description: További tudnivalók az IoT-eszközök konfigurálásához léptékű gyakorlati tanácsok
author: chrisgre
manager: briz
ms.author: chrisgre
ms.date: 06/24/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 7314c5ec05bec61e5bbbc406b6a39a7c5a8f011f
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036008"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Ajánlott eljárások az eszközök konfigurációját belül egy IoT-megoldás

Automatikus kezelés az Azure IoT Hub automatizálja számos ismétlődő és összetett feladat a nagy eszköz flották keresztül a teljes egészében a életciklusának kezelésére. Ez a cikk fejlesztésének és az IoT-megoldás operációs meghatározása a különböző szerepkörök pillanatképkészlethez bevált gyakorlatát.

* **Az IoT hardver gyártója/integráló:** gyártók az IoT-hardver, különböző gyártók vagy beszállítói hardver biztosítva az IoT központi telepítésének hardver összeállításakor kiegészítők gyártott, vagy más szállítók által integrált. Fejlesztési és a belső vezérlőprogram, a beágyazott operációs rendszerek és a beágyazott szoftverek integrációja részt.
* **Az IoT-megoldás fejlesztője:** egy IoT-megoldás fejlesztése általában egy megoldás fejlesztő végzi. A fejlesztői lehet egy belső fejlesztésű csoport vagy egy ezzel a tevékenységgel szakosodott rendszert integráló része. Az IoT-megoldás fejlesztői fejlesztése teljesen új IoT-megoldás különböző összetevői, szabványos vagy nyílt forráskódú összetevők integrálását, testre is szabhatók egy [IoT megoldásgyorsító][lnk-solution].
* **Az IoT-megoldás operátor:** után IoT-megoldás van telepítve, a hosszú távú működésre, ellenőrzésre, frissítések és karbantartás van szükség. Ezeket a feladatokat egy vállalaton belüli csapat információk informatikai szakemberek számára, a hardver műveletek és a karbantartási csoportok és a tartomány szakembereket alkalmaznak, akik a figyelheti az általános IoT-infrastruktúra helyes működése alkotó végezhető.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Az IoT-eszközök konfigurálásához léptékű automatikus Eszközkezelés ismertetése

Automatikus Eszközkezelés tartalmaz számos előnyének [eszköz twins] [ lnk-device-twins] és [modul twins] [ lnk-module-twins] szinkronizálni kívánt és jelentett állapotok a felhő és az eszközök között.  [Automatikus eszközkonfigurációk] [ lnk-auto-device-config] automatikusan twins nagy mennyiségű frissítése és summerize folyamat és annak megfelelőségét. A következő magas szintű lépések azt mutatják be automatikus Eszközkezelés fejleszti és használja:

* A **IoT hardver gyártója/integráló** eszközök felügyeleti funkciói belül egy beágyazott alkalmazást, amely megvalósítja [eszköz twins][lnk-device-twins]. Ezek a szolgáltatások tartalmazhatnak belső vezérlőprogram-frissítésekre, Szoftvertelepítés és a frissítés és beállítások kezelése.
* A **IoT-megoldás fejlesztői** valósítja meg a felügyeleti réteg eszköz felügyeleti műveletek használatával [eszköz twins] [ lnk-device-twins] és [automatikus eszköz konfigurációk][lnk-auto-device-config]. A megoldás tartalmaznia kell az eszköz felügyeleti feladatok elvégzésére műveletinterfészben meghatározása.
* A **IoT-megoldás operátor** használja az IoT-megoldás eszköz felügyeleti feladatok végrehajtása, különösen a csoportosíthatja az eszközöket együtt, kezdeményezni a konfigurációs módosítások, például a belső vezérlőprogram-frissítésekre, figyelemmel az előrehaladást, és hibaelhárítását problémák is, amelyeket merülhetnek fel.

## <a name="iot-hardware-manufacturerintegrator"></a>Az IoT hardver gyártója/integráló

Gyakorlati tanácsok a hardvergyártók és beágyazott szoftverfejlesztői foglalkozó kiegészítők a következők:

* **Alkalmazzon [eszköz twins][lnk-device-twins]:** eszköz twins engedélyezni kívánt konfiguráció a felhőből, és az aktuális konfiguráció és az eszköz tulajdonságok szinkronizálása.  A legjobb módja valósítja meg az eszköz twins beágyazott alkalmazásokon belül keresztül, a [Azure IoT SDK-k][lnk-azure-sdk].  Eszköz twins legmegfelelőbb konfigurációja, mivel azok: egy. kétirányú kommunikáció b támogatja. lehetővé teszi mindkét csatlakoztatott és a leválasztott eszköz állapotok c. Kövesse a végleges konzisztencia d elvét. a felhőben teljesen queriable
* **Az eszköz a két felügyeleti szerkezeti:** úgy, hogy az eszköz felügyeleti tulajdonságainak vannak logikailag egy csoportba tartoznak szakaszokra meghatározni az eszköz iker. Így lehetővé teszi a konfigurációs módosítások lehessen többi szakasza pedig a kettős befolyásolása nélkül. Hozzon létre például a belső vezérlőprogram, szoftverek, egy másik szakasz kívánt tulajdonságainak belül szakaszt, és a harmadik szakasz a hálózati beállításokat. 
* **Eszköz attribútumok, amelyek hasznosak a kezelés jelentést:** tulajdonságai, például a fizikai eszköz gyártmány, és a modell, belső vezérlőprogramok, operációs rendszer, sorozatszámát, és egyéb azonosítókhoz hasznos jelentéskészítésre és a célcsoport-kezelési paraméter konfigurációs módosítások.
* **A fő állapotok jelentéskészítés állapotának és előrehaladtának meghatározása:** legfelső szintű állapotok, hogy az operátornak jelenthetők vehető számba. Például a vezérlőprogram-frissítés állapota a jelenlegi, letöltés, alkalmazása, folyamatban lévő és hiba küldenek.  További mezők további információt az egyes határozza meg.  

## <a name="iot-solution-developer"></a>Az IoT-megoldás fejlesztői

Ajánlott eljárások az IoT megoldás fejlesztőknek által létrehozott Azure-alapú rendszerek a következők:

* **Alkalmazzon [eszköz twins][lnk-device-twins]:** eszköz twins engedélyezni kívánt konfiguráció a felhőből, és az aktuális konfiguráció és az eszköz tulajdonságok szinkronizálása.  A felhőalapú megoldások alkalmazásokon belül eszköz twins végrehajtásához legkönnyebben keresztül a [Azure IoT SDK-k][lnk-azure-sdk].  Eszköz twins legmegfelelőbb konfigurációja, mivel azok: egy. kétirányú kommunikáció b támogatja. lehetővé teszi mindkét csatlakoztatott és a leválasztott eszköz állapotok c. Kövesse a végleges konzisztencia d elvét. a felhőben teljesen queriable
* **Eszközök eszköz iker Címkék rendezése:** a megoldás lehetővé kell tennie az üzemeltető meghatározásához szolgáltatásminőségi körök vagy más beállítása az eszközök, például Kanári számos központi telepítési stratégia alapján. Eszköz szervezeten belül a megoldás eszköz iker címkék használatával valósítható és [lekérdezések][lnk-queries].  Eszköz szervezet pontosan és biztonságosan konfigurációs összegző elemek lehetővé kell tenni.
* **Alkalmazzon [automatikus eszközkonfigurációk][lnk-auto-device-config]:** automatikus eszköz-konfigurációk telepítése és a figyelési konfiguráció eszköz twins keresztül az IoT-eszközök nagy mennyiségű módosításait.  Automatikus eszközkonfigurációk cél eszköz twins keresztül készleteinek a **céloz feltétel,** amely eszközön lekérdezés iker címkék vagy jelentett tulajdonságok. A **céloz tartalom** lesz beállítva, a célzott eszközön twins belül kívánt tulajdonságokat készlete. A célként megadott tartalom határozzák meg az IoT hardver gyártója/integráló eszköz iker struktúrájú kell igazítása.  A **metrikák** eszköz iker lekérdezései jelentett tulajdonságait, és kell is megfelel-e az eszköz iker struktúra határozzák meg az IoT hardver gyártója/integráló. Automatikus eszközkonfigurációk is az előnye, hogy az IoT-központ eszköz iker műveleteket, amelyek soha nem haladhatja meg a díj a [szabályozás korlátok] [ lnk-throttling] eszköz iker Olvasás, mind a frissítéseket.
* **Használjon [eszköz kiépítése szolgáltatáshoz][lnk-dps]:** megoldás a fejlesztők által használandó az eszköz kiépítése szolgáltatáshoz eszköz iker címkék hozzárendelése az új eszközöket úgy, hogy azok automatikusan konfigurált **automatikus eszközkonfigurációk** , amely a címkét tartalmazó twins célozzák. 

## <a name="iot-solution-operator"></a>Az IoT-megoldás operátor

Az IoT megoldás üzemeltetői Azure IoT-megoldás épülő gyakorlati tanácsok a következők:

* **A felügyeleti eszközök rendszerezéséhez:** az IoT-megoldás kell határozza meg, vagy lehetővé teszik a minőségi körök létrehozását vagy más beállítása az eszközök, például Kanári számos központi telepítési stratégia alapján. A különböző eszközök fokozatosan konfigurációs módosításokat, és más a méretezési eszköz felügyeleti műveletek elvégzéséhez használható.
* **Szakaszos bevezetési konfigurációs módosítások végrehajtása:** szakaszos bevezetési egy általános eljárásait, amellyel operátor telepíti a módosításokat az IoT-eszközök kiterjesszük készlete. A célja, hogy a módosítások fokozatosan csökkenthető a kockázata, hogy széles méretezési megszakítása a módosítások elvégzéséhez.  Az operátor létrehozásához kell használnia a megoldás felület egy [automatikus eszközkonfiguráció][lnk-auto-device-config], és a célcsoport-kezelési feltételt kell egy kezdeti készleteinek eszközök (például egy Kanári csoport).  Az operátor ezt követően kell ellenőrizni kell az eszközök kezdeti készleteinek konfiguráció módosítása.  Érvényesítés végrehajtása után a következő operátor frissíteni fogja az automatikus konfigurációja eszközök nagyobb készlete tartalmazza. Az operátor is kell beállítani a a konfiguráció magasabb, mint más konfigurációs jelenleg céloz meg az eszközöket.  A bevezetési a metrikák az automatikus konfigurációja által jelentett segítségével követhetők nyomon. 
* **Hajtsa végre a hibák vagy konfigurációs hibák esetén visszagörgetése:** egy automatikus konfigurációs okozó hibák vagy hibás konfigurációja is vissza lesz vonva módosításával a **feltétel célzó** , hogy az eszközök nem már megfelel a célcsoport-kezelési feltételnek.  Győződjön meg arról, hogy egy másik automatikus eszközkonfiguráció alacsonyabb prioritású továbbra is érvényes az ilyen eszközökön.  Győződjön meg arról, hogy a visszaállítás sikeres volt. a metrikák megtekintésével: A állítva visszaírt konfigurációs állapotúnak kell lennie már nem untargeted eszközökhöz, és a második konfigurációs metrikák most tartalmaznia kell az eszközöket, amelyek továbbra is hány példányban.


## <a name="next-steps"></a>További lépések

* További tudnivalók az eszköz twins végrehajtási [megértése és -felhasználási eszköz twins az IoT hubon][lnk-device-twins]
* Lépésről lépésre létrehozása, frissítése vagy egy automatikus konfiguráció törlése bemutatjuk [konfigurálása és figyelheti az IoT-eszközök léptékű][lnk-auto-device-config].
* Valósítja meg az eszköz twins és automatikus konfiguráció a belső vezérlőprogram frissítési mintát [oktatóanyag: egy eszköz belső vezérlőprogram frissítési folyamat végrehajtása][lnk-firmware-update].

<!-- Links -->
[lnk-device-twins]: iot-hub-devguide-device-twins.md
[lnk-module-twins]: iot-hub-devguide-module-twins.md
[lnk-auto-device-config]: iot-hub-auto-device-config.md
[lnk-firmware-update]: tutorial-firmware-update.md
[lnk-throttling]: iot-hub-devguide-quotas-throttling.md
[lnk-queries]: iot-hub-devguide-query-language.md
[lnk-dps]: ../iot-dps/how-to-manage-enrollments.md
[lnk-azure-sdk]: https://github.com/Azure/azure-iot-sdks
[lnk-solution]: https://azure.microsoft.com/features/iot-accelerators/