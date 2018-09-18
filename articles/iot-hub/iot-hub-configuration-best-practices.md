---
title: Eszköz konfigurációs ajánlott eljárások az Azure IoT Hub |} A Microsoft Docs
description: Nagy mennyiségű IoT-eszköz konfigurálásával kapcsolatos bevált gyakorlatok megismeréséhez
author: chrisgre
ms.author: chrisgre
ms.date: 06/24/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 5eb0ba659961d809d0ae471034b03263f87e3894
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985498"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Ajánlott eljárások az IoT-megoldás belüli eszközök konfigurálása

Az automatikus kezelés az Azure IoT Hub nagy eszköz flották kezelésének azok életciklusának teljes keresztül sok ismétlődő és összetett feladatokat automatizálja. Ez a cikk határozza meg az érintett a különféle szerepkörök vonatkozó ajánlott eljárásokat számos fejlesztésének és működő IoT-megoldás.

* **IoT hardver gyártója/integráló:** az IoT-gyártók hardveres, hardver összeállítása a különböző gyártók vagy szállítók hardver kezeléséről egy IoT-környezet rendszerintegrátorok gyártott vagy más szállítók által integrált. Fejlesztés és a belső vezérlőprogram, embedded operációs rendszerek és beágyazott szoftverekért integrációja részt.

* **IoT-megoldás fejlesztői:** IoT-megoldások fejlesztésének általában végzi el a megoldás fejlesztőjének. A fejlesztői-csapat vagy a tevékenység-fókuszú rendszerintegrátor része lehet. Az IoT-megoldás fejlesztőjének hozhat létre különböző összetevői az IoT-megoldás teljesen új, integrálni a különböző standard vagy a nyílt forráskódú összetevők, és testre szabhatja egy [IoT-megoldásgyorsítók](/azure/iot-accelerators/).

* **IoT-megoldás operátor:** után az IoT-megoldás üzemel, a hosszú távú működésre, monitorozásra, frissítések és karbantartás van szükség. Ezeket a feladatokat egy csapat információk technológiai szakemberek, hardver műveletek és karbantartás csapatok és tartomány szakértőitől, akik a helyes működése az általános IoT-infrastruktúra figyelése hajtható végre.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Nagy mennyiségű IoT-eszközök konfigurálása automatikus Eszközfelügyelet ismertetése

Az automatikus kezelés magában foglalja számos előnyének [ikereszközök](iot-hub-devguide-device-twins.md) és [ikermodulokkal](iot-hub-devguide-module-twins.md) kívánt és jelentett állapota a felhő és az eszközök közötti szinkronizálását. [Automatikus eszköz-konfigurációk] [lnk-automatikus eszköz-konfiguráció] automatikusan twins nagyméretű adatkészletek frissítése és summerize folyamat és a megfelelőség. A következő magas szintű lépéseket ismertetik az automatikus kezelés fejlesztett ki és használja:

* A **IoT hardver gyártója/integráló** eszközfelügyeleti funkcióival belül egy beágyazott alkalmazás használatával valósítja meg [ikereszközök](iot-hub-devguide-device-twins.md). Ezek a szolgáltatások tartalmazhatnak belső vezérlőprogram frissítése, a Szoftvertelepítés és a frissítés és a beállítások kezelése.

* A **IoT-megoldás fejlesztői** valósítja meg a felügyeleti réteg az eszközfelügyeleti műveleteknek használatával [ikereszközök](iot-hub-devguide-device-twins.md) és [automatikus eszközkonfigurációkat](iot-hub-auto-device-config.md). A megoldás meghatározása az eszköz felügyeleti feladatok elvégzésére operátor illesztőfelületet tartalmaznia kell.

* A **IoT-megoldás operátor** használja az IoT-megoldás az eszköz felügyeleti feladatok végrehajtásához, különösen a csoportosíthatja az eszközöket együtt, kezdeményezheti konfigurációs módosítások, például a belső vezérlőprogram frissítése, előrehaladásának figyeléséhez és hibaelhárításához problémák, amelyeket merülhetnek fel.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT hardver gyártója/rendszerintegrátor

Ajánlott eljárások a hardvergyártók és a beágyazott szoftverfejlesztői foglalkozó rendszerintegrátorok a következők:

* **Alkalmazzon [ikereszközök](iot-hub-devguide-device-twins.md):** ikereszközök engedélyezése szinkronizálása a felhőből, és az aktuális konfigurációja és az eszköz tulajdonságok jelentéshez szükséges konfiguráció. A legjobb módja ikereszközök belül az embedded-alkalmazások megvalósításához, keresztül a [Azure IoT SDK-k](https://github.com/Azure/azure-iot-sdks). Ikereszközök configuration leginkább kihasználni, mert ezek:

    * Támogatja a kétirányú kommunikációt.
    * Lehetővé teszi mindkét csatlakoztatott és a leválasztott Eszközállapotok.
    * Kövesse a végleges konzisztencia elvét.
    * A rendszer teljes mértékben lekérdezhető a felhőben.

* **Az ikereszközök eszközfelügyeleti struktúra:** az ikereszköz úgy, hogy az eszköz felügyeleti tulajdonságok vannak logikailag szakaszokra válaszadatainak felépítését. Ez lehetővé teszi a konfigurációs módosítások kell különíteni az ikereszköz többi szakasza befolyásolása nélkül. Hozzon létre például egy szakaszt, kívánt tulajdonságok a belső vezérlőprogram, szoftverek, egy másik szakaszt, és a harmadik szakasz a hálózati beállításokat. 

* **Jelentés az eszköz attribútumait, amelyek hasznosak a felügyelethez:** attribútumok, például a fizikai eszköz győződjön meg arról, és a modell, a belső vezérlőprogram, operációs rendszer, sorozatszám, és egyéb azonosítókhoz kötött hasznos jelentéskészítésre és célcsoport-kezelési paraméterek konfigurációs módosítások.

* **A fő állapotának és előrehaladtának reporting állapotokat határozzák:** legfelső szintű állapotok számba kell, hogy az operátor jelenteni is lehet. Például a belső vezérlőprogram frissítésének aktuális, letöltése, alkalmazása, folyamatban lévő és a hiba állapot szeretne jelentést. További információt a további mezőket meg az egyes állapotokhoz.

## <a name="iot-solution-developer"></a>IoT-megoldás fejlesztői

Ajánlott eljárások az IoT megoldás a fejlesztőknek, akik készítenek az Azure-alapú rendszerek a következők:

* **Alkalmazzon [ikereszközök](iot-hub-devguide-device-twins.md):** ikereszközök engedélyezése szinkronizálása a felhőből, és az aktuális konfigurációja és az eszköz tulajdonságok jelentéshez szükséges konfiguráció. A legjobb módja, ikereszközök belül a felhőalkalmazások megoldások megvalósításának keresztül, a [Azure IoT SDK-k](https://github.com/Azure/azure-iot-sdks). Ikereszközök configuration leginkább kihasználni, mert ezek:

    * Támogatja a kétirányú kommunikációt.
    * Lehetővé teszi mindkét csatlakoztatott és a leválasztott Eszközállapotok. 
    * Kövesse a végleges konzisztencia elvét.
    * A rendszer teljes mértékben lekérdezhető a felhőben.

* **Rendezik az eszközöket device twin címkékkel:** a megoldás lehetővé teszi az üzemeltető minőségi körök vagy más eszközök Kanári például számos központi telepítési stratégia alapján részhalmazához. Eszköz szervezet valósítható meg a device twin címkék használatával megoldáson belül, és [lekérdezések](iot-hub-devguide-query-language.md). Eszköz szervezet szükség, hogy a konfigurációs összegző mintáit, biztonságosan és nagy pontossággal.

* **Alkalmazzon [automatikus eszközkonfigurációk](iot-hub-auto-device-config.md):** automatikus eszköz-konfigurációk üzembe helyezése és a nagy mennyiségű IoT-eszközöket az ikereszközök figyelő konfigurációs módosításait. Automatikus eszközkonfigurációkat célként az ikereszközök használatával beállítja a **feltétel, cél** ami az a lekérdezés eszköz az ikereszköz – címkék vagy a jelentett tulajdonságok. A **tartalmat** belül a célzott eszközök ikerállapotának beállított kívánt tulajdonságokat készlete. A célként megadott tartalom eszköz ikereszköz struktúráját határozza meg az IoT hardver gyártója/integráló kell igazítani. 

   A **metrikák** ikereszköz-lekérdezéseket a jelentett tulajdonságok és kell igazítani, az eszköz ikereszköz struktúrával határozzák meg az IoT hardver gyártója/integráló is. Automatikus eszköz-konfigurációk is, hogy az előnye, hogy az IoT Hub device twin műveleteket gyakorisággal, amely soha nem haladhatja meg a [szabályozási korlátait](iot-hub-devguide-quotas-throttling.md) eszköz ikerírási és frissítéseket.

* **Használja a [Device Provisioning Service](../iot-dps/how-to-manage-enrollments.md):** megoldás fejlesztők használjon a Device Provisioning Service eszköz ikereszköz címkéket társíthat az új eszközökre, hogy azok automatikusan megtörténik a  **automatikus eszközkonfigurációkat** , amelyek adott címkével rendelkező twins célozzák. 

## <a name="iot-solution-operator"></a>IoT-megoldás operátor

Ajánlott eljárások az IoT-megoldás kezelőknek ki Azure-alapú IoT-megoldás használatával a következők:

* **A felügyeleti eszközök rendszerezéséhez:** az IoT-megoldás kell definiálni, vagy lehetővé teszik a minőségi körök létrehozása vagy eszközök alapján különböző üzembe helyezési stratégiáknak felelnek Kanári például más részhalmazához. A különböző eszközök vezethet be konfigurációs módosítások és az egyéb ipari méretekben eszköz felügyeleti műveletek végrehajtásához használható.

* **Szakaszos bevezetés használatával konfigurációs módosítások végrehajtása:** szakaszos bevezetés egy általános eljárásait, amellyel az operátornak telepíti a módosítások egy IoT-eszközök kiterjesszük készletét. A célja az, hogy a parancsban történt használhatatlanná tévő széles skálán kockázatának csökkentése érdekében fokozatosan módosítsa.  Az operátor létrehozásához kell használnia a megoldás felület egy [automatikus eszközkonfiguráció](iot-hub-auto-device-config.md) és a célcsoport-kezelési feltétel eszközök (például egy tesztcsoportos csoportot) egy kezdeti készleteinek irányul. Az operátor majd ellenőrizni kell az eszközök kezdeti készleteinek konfiguráció módosítása. 

   Érvényesítés befejezése után a operátort frissíteni fogja az automatikus eszközkonfiguráció eszközök nagyobb készlete tartalmazza. Az operátor is kell beállítani a a konfiguráció magasabb, mint az egyéb konfigurációk, az adott eszközökre való jelenleg vonatkozik. A bevezetési a metrikák az automatikus eszközkonfiguráció által jelentett használatával figyelhető meg. 

* **Hajtsa végre a hibák vagy konfigurációs hibák esetén visszagörgetése:** egy automatikus konfigurációt, amely a hibák vagy konfigurációs hibák miatt is vissza lesz állítva módosításával a **feltétel célzó** úgy, hogy az eszközök nem már megfelel a célcsoport-kezelési feltételnek. Győződjön meg arról, hogy egy másik automatikus eszközkonfiguráció alacsonyabb prioritású eszközök továbbra is vonatkozik. Győződjön meg arról, hogy a visszaállítás sikeres volt-e a mérőszámok megtekintésével: A helyzetben előzőre konfigurációs állapotúnak kell lennie már nem untargeted eszközökhöz, és a második konfigurációs metrikák most már tartalmazza a továbbra is célzott eszközök számát.

## <a name="next-steps"></a>További lépések

* Ismerje meg az ikereszközöket végrehajtási [ikereszközök megismerése és használata az IoT Hub](iot-hub-devguide-device-twins.md).

* Lépésről lépésre történő létrehozásához, frissítéséhez vagy törléséhez egy, az automatikus eszközkonfiguráció [konfigurálása és figyelése a nagy mennyiségű IoT-eszközök](iot-hub-auto-device-config.md).

* Egy belső vezérlőprogram frissítési minta az ikereszközök és az automatikus eszközkonfigurációkat megvalósítása [oktatóanyag: egy eszköz belső vezérlőprogram frissítési folyamata megvalósítása](tutorial-firmware-update.md). 
