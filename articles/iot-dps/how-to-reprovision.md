---
title: Hogyan kell építenie az eszközök az Azure IoT Hub Device Provisioning Service |} A Microsoft Docs
description: Hogyan kell építenie az eszközök a device provisioning service-példány
author: wesmc7777
ms.author: wesmc
ms.date: 08/15/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 36f919d1c22a88dfaf13079f09e6a43980a22828
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981780"
---
# <a name="how-to-reprovision-devices"></a>Hogyan kell építenie az eszközök

IoT-megoldás élettartama során szokás az eszközök IoT-központok között. Az erről az áthelyezésről okok a következők lehetnek a következő esetekben:

* **Földrajzi hely meghatározásának**: egy eszköz között helyezi át, mert az eszköz által növelése a hálózati késés át egy IoT hubra közelebb és az egyes helyekre.

* **Több-bérlős**: eszköz belül az IoT-megoldásban használt, de hozzárendelni vagy egy új ügyfél vagy ügyfél telephelyén bérelt. Az új ügyféllel előfordulhat, hogy szolgálja ki a különböző IoT hub használatával.

* **Megoldás módosítása**: eszköz sikerült áthelyezni egy új vagy frissített IoT-megoldás. Az újbóli hozzárendelést megkövetelheti, hogy az eszköz kommunikálni egy új IoT hubot, amely más háttérrendszer összetevők csatlakozik. 

* **Karantén**: hasonló, hogy a megoldás módosítása. Egy IoT hubra, ahol teheti meg, csak frissítse, majd az megfelelőségi hibás, sérült vagy elavult eszköz rendelésekor lehet. Miután az eszköz megfelelően működik-e, majd át térjen vissza a fő hub.

További részletes áttekintést reprovisioning, lásd: [IoT Hub Device reprovisoning fogalmak](concepts-device-reprovision.md).


## <a name="configure-the-enrollment-allocation-policy"></a>A regisztrációs kiosztási szabályzat konfigurálása

A kiosztási szabályzat határozza meg, hogyan társított a regisztrációs eszköz kell lefoglalva, vagy rendelve egy IoT hubot, egyszer kiépíteni.

Az alábbi lépéseket a kiosztási szabályzat egy eszközök beléptetéséhez konfigurálása:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és keresse meg a Device Provisioning Service-példányhoz.

2. Kattintson a **beléptetések kezelése**, és kattintson a regisztrációs csoport vagy reprovisioning a konfigurálni kívánt egyéni regisztráció. 

3. A **válassza ki, hogyan szeretné hozzárendelni az eszközöket hubs**, válasszon egyet az alábbi foglalási szabályzatok:

    * **Legkisebb késés**: Ez a szabályzat eszközök rendel a kapcsolt IoT hubot, amely azt eredményezi a legkisebb késés kommunikációs eszköz és az IoT Hub között. Ez a beállítás lehetővé teszi, hogy az eszközt a legközelebbi helyen alapuló IoT hubbal való kommunikációhoz. 
    
    * **Egyenletesen súlyozott elosztás**: Ez a szabályzat elosztja a foglalási súly rendelt egyes kapcsolt IoT hubot alapján összekapcsolt IoT-központok eszközök. Ez a szabályzat lehetővé teszi, hogy egyenleg eszközök elosztását a foglalási súlyok ezeket hubs beállítása alapján összekapcsolt központok csoportja. Ha csak egy IoT Hub-eszközök szeretne kiépíteni, ezt a beállítást javasoljuk. Ez az alapértelmezett beállítás. 
    
    * **Statikus konfiguráció**: A házirend feltétele, hogy a kívánt IoT-központ a kiépítendő eszköz regisztrációs bejegyzés szerepel. Ez a szabályzat lehetővé teszi, hogy egyetlen konkrét IoT hubra, amelyet szeretne hozzárendelni az eszközöket a.

4. A **válassza ki az IoT-központok ennek a csoportnak rendelhetők**, válassza ki az összekapcsolt IoT-központok, amelyeket szeretne a foglalási szabályzat is. Opcionálisan adja hozzá az új csatolt Iot hub használata a **egy új IoT Hub csatolása** gombra.

    Az a **legkisebb késés** kiosztási szabályzat a kiválasztott hubs fog szerepelni a késés kiértékelése a legközelebbi hub, az eszköz-hozzárendeléshez meghatározásához.

    Az a **egyenletesen súlyozott elosztás** kiosztási szabályzat fogja eszközöket kell osztani a beállított felosztási súlyukat alapján választja hubs és a jelenlegi eszköz terhelés.

    Az a **statikus konfiguráció** foglalási szabályzatot – válassza ki az IoT hub azt szeretné, hogy a hozzárendelt eszközökön.

4. Kattintson a **mentése**, vagy folytassa a következő szakaszban reprovisioning házirend beállítása.

    ![Válassza ki a regisztrációs foglalási szabályzat](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>A reprovisioning szabályzat beállítása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és keresse meg a Device Provisioning Service-példányhoz.

2. Kattintson a **beléptetések kezelése**, és kattintson a regisztrációs csoport vagy reprovisioning a konfigurálni kívánt egyéni regisztráció.

3. A **válassza ki, hogyan szeretné kezelni a különböző IoT hubra újrakiépítés eszközön lévő adatokat**, válasszon egyet az alábbi reprovisioning házirendek:

    * **Újbóli üzembe helyezése és az adatok áttelepítése**: Ez a szabályzat művelet vesz igénybe, ha a regisztrációs bejegyzés társított eszközök elküld egy új kiépítési kérést. A regisztrációs bejegyzés konfigurációjától függően az eszköz lehet hozzárendelni egy másik IoT hubra. Ha az eszköz IoT-központok változnak, az eszköz regisztrációját, a kezdeti az IoT hub távolítja el. Az új IoT hub összes Eszközállapot-adatokat a kezdeti IoT hubról lesz át. Az áttelepítés során az eszköz állapota nem szerepel, **hozzárendelése**

    * **Újbóli üzembe helyezése és a kezdeti konfiguráció alaphelyzetbe**: Ez a szabályzat művelet vesz igénybe, ha a regisztrációs bejegyzés társított eszközök elküld egy új kiépítési kérést. A regisztrációs bejegyzés konfigurációjától függően az eszköz lehet hozzárendelni egy másik IoT hubra. Ha az eszköz IoT-központok változnak, az eszköz regisztrációját, a kezdeti az IoT hub távolítja el. A kezdeti konfigurációs adatokat, a kiépítési szolgáltatás példány kapta, amikor az eszköz lett üzembe helyezve az új IoT hub által biztosított. Az áttelepítés során az eszköz állapota nem szerepel, **hozzárendelése**.

4. Kattintson a **mentése** ahhoz, hogy az eszköz a módosítások alapján reprovisioning.

    ![Válassza ki a regisztrációs foglalási szabályzat](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Egy kiépítési kérést küldhet az eszközről

Ahhoz, hogy érvénybe eszközök alapján az előző szakaszok végzett konfigurációmódosítások, ezek az eszközök kell igényelnie reprovisioning. 

A forgatókönyvtől függ, hogy milyen gyakran egy eszközt a kiépítési kérést küld. Azonban javasolt az eszközök egy kiépítési kérést küldhet a kiépítési szolgáltatás példánya újraindítás és a támogatás a program egy [metódus](../iot-hub/iot-hub-devguide-direct-methods.md) manuálisan indítható a igény szerinti kiépítés. Kiépítés sikerült is elindítható a beállítás egy [kívánt tulajdonság](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example). 

A regisztrációs bejegyzés reprovisioning házirend határozza meg, hogyan kezeli az eszközt a kiépítési szolgáltatáspéldány a regisztrációs kérések, és ha eszközadatok állapot reprovisioning során lesz áttelepítve. Az ugyanazon házirend érhető el az egyéni regisztrációk és regisztrációs csoportok:

Például a kód küldése kiépítési kérelmeket az eszközről a rendszerindítási folyamat során lásd [szimulált eszköz automatikus kiépítés](quick-create-simulated-device.md).


## <a name="next-steps"></a>További lépések

- További Reprovisioning kapcsolatban lásd: [IoT Hub Device reprovisoning fogalmak](concepts-device-reprovision.md) 
- Megszüntetés további tudnivalókért lásd: [hogyan eszközöket, amelyek korábban automatikus – kiépített megszüntetése ](how-to-unprovision-devices.md) 











