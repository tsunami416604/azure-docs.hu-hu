---
title: Eszközök újbóli kiépítése az Azure IoT Hub-eszközkiépítési szolgáltatásban
description: Ismerje meg, hogyan építheti ki újra az eszközöket az eszközkiépítési szolgáltatás (DPS) példányával, és miért lehet, hogy ezt kell tennie.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0ded494debab19daa15a953715b1ab7b0b10ad18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974904"
---
# <a name="how-to-reprovision-devices"></a>Eszközök újbóli kiépítése

Az IoT-megoldás életciklusa során gyakori az eszközök áthelyezése az IoT-központok között. Ennek a lépésnek az okai a következő forgatókönyvek lehetnek:

* **Földrajzi hely:** Ahogy az eszköz a helyek között mozog, a hálózati késés javul azáltal, hogy az eszközt az egyes helyekhez közelebb iT-hubba telepíti át.

* **Több-bérlős:** Egy eszköz használható ugyanazon az IoT-megoldáson belül, de átrendelhető vagy bérelt egy új ügyfél, vagy az ügyfél helyén. Ez az új ügyfél lehet kiszolgálni egy másik IoT hub használatával.

* **Megoldásmódosítása:** Egy eszköz áthelyezhető egy új vagy frissített IoT-megoldásba. Ez az újrahozzárendelés megkövetelheti, hogy az eszköz kommunikáljon egy új IoT hub, amely más háttér-összetevők höz csatlakozik. 

* **Karantén**: A megoldásmódosításhoz hasonlóan. Egy eszköz, amely hibásan működik, feltört, vagy elavult lehet hozzárendelni egy IoT hub, ahol minden, amit tehet, hogy frissítse, és kap vissza a megfelelőségi. Miután az eszköz megfelelően működik, a rendszer visszatelepíti a fő elosztóba.

Az újraépítés részletesebb áttekintését az [IoT Hub-eszköz újrakiépítése fogalmai című témakörben találja.](concepts-device-reprovision.md)


## <a name="configure-the-enrollment-allocation-policy"></a>A beléptetési hozzárendelési házirend konfigurálása

A foglalási szabályzat határozza meg, hogy a regisztrációhoz társított eszközök hogyan lesznek lefoglalva vagy hozzárendelve egy IoT-központhoz, miután újra kiosztották.

A következő lépések konfigurálják az eszköz regisztrációjának foglalási házirendjét:

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg az eszközkiépítési szolgáltatás példányát.

2. Kattintson **a Regisztrációk kezelése gombra,** majd kattintson a regisztrációhoz vagy az egyéni regisztrációhoz, amelyet újrakiépíteni szeretne. 

3. A **Válassza ki, hogyan szeretné hozzárendelni az eszközöket a hubokhoz,** válasszon az alábbi foglalási házirendek közül:

    * **Legalacsonyabb késés:** Ez a szabályzat eszközöket rendel a csatolt IoT Hubhoz, amely az eszköz és az IoT Hub közötti legalacsonyabb késéses kommunikációt eredményez. Ez a beállítás lehetővé teszi, hogy az eszköz a hely alapján kommunikáljon a legközelebbi IoT hubbal. 
    
    * **Egyenletesen súlyozott disztribúció:** Ez a szabályzat az egyes összekapcsolt IoT-központokhoz rendelt foglalási súly alapján osztja el az eszközöket a csatolt IoT-központok között. Ez a házirend lehetővé teszi az elosztóeszközök terhelését a csatolt hubok egy csoportjában az ezeken a csomópontokon beállított foglalási súlyok alapján. Ha csak egy IoT Hubra létesít erről a beállításról, azt javasoljuk, hogy ezt a beállítást. Ez az alapértelmezett beállítás. 
    
    * **Statikus konfiguráció:** Ez a házirend megköveteli, hogy a kívánt IoT Hub szerepel a regisztrációs bejegyzés egy eszköz kiépítésre. Ez a házirend lehetővé teszi, hogy egyetlen adott IoT-hubot jelöljön ki, amelyhez eszközöket szeretne hozzárendelni.

4. A **Csoport által hozzárendelhető IoT-központok kiválasztása**csoportban válassza ki azokat a csatolt IoT-központokat, amelyeket a foglalási szabályzatba be szeretne vonni. Szükség esetén adjon hozzá egy új, összekapcsolt iot hubot az **Új IoT Hub-kapcsolat tal** gombbal.

    A **legalacsonyabb késés-hozzárendelési** házirend, a kiválasztott csomópontok szerepelni fognak a késés kiértékelése az eszköz-hozzárendelés legközelebbi hub meghatározásához.

    Az **egyenletesen súlyozott elosztási** foglalási szabályzattal az eszközök terheléselosztása a kiválasztott hubok között a beállított elosztási súlyuk és az aktuális eszközterhelés alapján lesznek kiegyensúlyozva.

    A **statikus konfigurációs** foglalási házirend, válassza ki az IoT hub, amelyhez eszközöket szeretne rendelni.

4. Kattintson **a Mentés gombra,** vagy folytassa a következő szakaszsal az újrakiépítési házirend beállításához.

    ![Igénylési hozzárendelési házirend kiválasztása](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Az újrakiépítési házirend beállítása

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg az eszközkiépítési szolgáltatás példányát.

2. Kattintson **a Regisztrációk kezelése gombra,** majd kattintson a regisztrációhoz vagy az egyéni regisztrációhoz, amelyet újrakiépíteni szeretne.

3. A **Válassza ki, hogyan szeretné kezelni az eszközadatokat egy másik IoT-központba való újbóli üzembe építéskor,** válasszon az alábbi újraépítési szabályzatok közül:

    * **Adatok újbóli kiépítése és áttelepítése**: Ez a házirend akkor lép működésbe, ha a regisztrációs bejegyzéshez társított eszközök új létesítési kérelmet nyújtanak be. Attól függően, hogy a regisztrációs bejegyzés konfigurációját, az eszköz lehet hozzárendelni egy másik IoT hub. Ha az eszköz módosítja az IoT-központok, az eszköz regisztrációja a kezdeti IoT hub lesz távolítva. A kezdeti IoT hubról származó összes eszközállapot-információ átkerül az új IoT-központba. Az áttelepítés során az eszköz állapota **a hozzárendelés**

    * **Újraüzembe helyezés és visszaállítás a kezdeti konfigurációra**: Ez a házirend akkor lép működésbe, amikor a regisztrációs bejegyzéshez társított eszközök új létesítési kérelmet nyújtanak be. Attól függően, hogy a regisztrációs bejegyzés konfigurációját, az eszköz lehet hozzárendelni egy másik IoT hub. Ha az eszköz módosítja az IoT-központok, az eszköz regisztrációja a kezdeti IoT hub lesz távolítva. A kezdeti konfigurációs adatokat, amelyek a létesítési szolgáltatáspéldány kapott, amikor az eszköz kiépítése az új IoT hub. Az áttelepítés során az eszköz állapota **hozzárendelésként lesz jelentve.**

4. Kattintson a **Mentés** gombra az eszköz módosítások alapján történő újbóli kiépítésének engedélyezéséhez.

    ![Igénylési hozzárendelési házirend kiválasztása](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Kiépítési kérelem küldése az eszközről

Annak érdekében, hogy az eszközök az előző szakaszokban végrehajtott konfigurációs módosítások alapján újra kiépülhessenek, ezeknek az eszközöknek újraki kell építeniük. 

Az, hogy egy eszköz milyen gyakran küld kiépítési kérelmet, a forgatókönyvtől függ. Azonban azt javasoljuk, hogy az eszközök úgy programozhatja be, hogy egy kiépítési kérelmet küldjenek egy kiépítési szolgáltatáspéldánynak újraindításkor, és támogassanak egy [módszert](../iot-hub/iot-hub-devguide-direct-methods.md) az igény szerinti kiépítés manuális aktiválásához. A kiépítés a [kívánt tulajdonság](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example)beállításával is kiváltható. 

A regisztrációs bejegyzés reprovisioning házirendje határozza meg, hogy az eszközkiépítési szolgáltatáspéldány hogyan kezeli ezeket a létesítési kérelmeket, és ha az eszköz állapotadatait át kell telepíteni az újraépítés során. Ugyanazok a házirendek érhetők el az egyes beléptetési csoportok és beléptetési csoportok esetében:

Például a kiépítési kérelmek küldésének kódja egy eszközről egy rendszerindítási sorrendben, lásd: [Automatikus kiépítése egy szimulált eszköz.](quick-create-simulated-device.md)


## <a name="next-steps"></a>További lépések

- További újraépítés, lásd: [IoT Hub-eszköz újrakiépítése fogalmak](concepts-device-reprovision.md) 
- További kiépítés: A [korábban automatikusan kiépített eszközök kiirtása](how-to-unprovision-devices.md) 











