---
title: Az Azure IoT Hub-Eszközfelügyelet áttekintése |} A Microsoft Docs
description: Az Eszközfelügyelet az Azure IoT Hu--áttekintése vállalati eszközök életciklusa és eszközfelügyeleti minták, mint például újraindítás, gyári beállítások visszaállítása, belső vezérlőprogram frissítése, konfigurációs, ikereszközök, lekérdezések, feladatok.
author: bzurcher
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: briz
ms.openlocfilehash: bdc55af23568b5785a831e81f352400c728c902e
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043584"
---
# <a name="overview-of-device-management-with-iot-hub"></a>Az IoT Hub-eszközfelügyelet áttekintése

Az Azure IoT Hub által biztosított szolgáltatások és bővíthetőségi modell segítségével az eszköz- és háttérrendszer-fejlesztők hatékony eszközfelügyeleti megoldásokat építhetnek ki. Az eszközök köre a korlátozott érzékelőktől és az egycélú mikrovezérlőktől az eszközcsoportok kommunikációjának továbbítását végző, nagyteljesítményű átjárókig terjed.  Emellett az IoT-műveletek használati módja és követelményei az egyes iparágak esetében jelentősen eltér.  Ezen eltérések ellenére az Azure IoT Hub-eszközfelügyelet az eszközök és végfelhasználók változatos együttesének igényeihez igazítva biztosítja a szükséges képességeket, mintákat és kódelemtárakat.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

A vállalati IoT-megoldások sikeres létrehozásának fontos része annak a stratégiának a megalkotása, amely meghatározza a kezelők számára a folyamatos eszközfelügyelet módját. Az IoT-kezelőknek olyan egyszerű és megbízható eszközökre és alkalmazásokra van szükségük, amelyek segítségével a feladataik stratégiai szempontjaira koncentrálhatnak. Ez a cikk a következő információkat tartalmazza:

* Az Azure IoT Hub-eszközfelügyelet rövid áttekintése.
* Az általános eszközfelügyeleti alapelvek leírása.
* Az eszközök életciklusának ismertetése.
* Az általános eszközfelügyeleti minták áttekintése.

## <a name="device-management-principles"></a>Az eszközfelügyelet alapelvei

Az IoT alkalmazása egyedi eszközfelügyeleti kihívásokat hoz magával, a következő alapelveket pedig a vállalati szintű megoldások mindegyikének magában kell foglalnia:

![Az eszközfelügyelet alapelveinek ábrája](media/iot-hub-device-management-overview/image4.png)

* **Méretezés és automatizálás**: Az IoT-megoldások olyan egyszerű eszközök alkalmazását igénylik, amelyek képesek a rutinfeladatok automatizálására, valamint amelyekkel eszközök millióinak kezelése oldható meg viszonylag kis kezelői létszámmal. A kezelők napról napra azt várják, hogy távolról, kötegelten kezelhessék az eszközök működését, és a rendszer csak akkor riassza őket, ha olyan hiba történt, amelynek elhárításához a közvetlen beavatkozásukra van szükség.

* **Nyitottság és kompatibilitás**: az eszközök ökoszisztémája rendkívül szerteágazó. A felügyeleti eszközöket úgy kell kialakítani, hogy alkalmasak legyenek nagy mennyiségű eszközosztály, platform és protokoll befogadására. A kezelőknek képesnek kell lenniük számos eszköztípus kezelésére, a legkorlátozottabb, beágyazott egyutas lapkáktól kezdve a nagyteljesítményű, teljeskörű funkciókkal ellátott számítógépekig.

* **A környezet figyelése**: az IoT-környezetek dinamikusak és állandóan változnak. A szolgáltatás megbízhatósága létfontosságú. Az eszközfelügyeleti műveleteknek a következő tényezőket kell figyelembe venniük annak biztosításához, hogy a karbantartási állásidő ne befolyásolja a létfontosságú üzleti tevékenységeket, illetve ne idézzen elő veszélyes feltételeket:

    * SLA karbantartási időszakok
    * A hálózat és a tápellátás állapota
    * Használatban lévő feltételek
    * Az eszköz földrajzi helyei

* **Sok szerepkör kiszolgálása**: az IoT-műveletekhez tartozó szerepkörök egyedi munkafolyamatainak és folyamatainak támogatása rendkívül fontos. Az üzemeltetést végző stábnak zökkenőmentesen kell együttműködnie a belső informatikai részlegek által meghatározott korlátozásokkal.  Fenntartható módot kell találniuk továbbá a valós idejű eszközművelet-információk beillesztésére a felettesi és üzletfelügyeleti szerepkörökbe.

## <a name="device-lifecycle"></a>Az eszközök életciklusa
Egyes általános eszközfelügyeleti szakaszok minden vállalati IoT-projektben megjelennek. Az Azure IoT-ben az eszközök életciklusának öt szakaszát különböztetjük meg:

![Az Azure IoT-eszközök életciklusának öt fázisa: tervezés, üzembe helyezés, konfigurálás, figyelés, kivonás](./media/iot-hub-device-management-overview/image5.png)

A teljes megoldás megvalósításához számos eszközkezelői követelménynek kell teljesülnie ezen az öt szakaszon belül:

* **Tervezés**: Lehetővé teszi a kezelők számára olyan eszközmetaadat-séma létrehozását, amellyel egyszerű és pontos lekérdezés végezhető el egy adott eszközcsoporton a tömeges felügyeleteken. Az ikereszközök használatával e metaadatokat címkék és tulajdonságok formájában tárolhatja.
  
    *További információk*: 
    * [Ikereszközök – első lépések](iot-hub-node-node-twin-getstarted.md)
    * [Az ikereszközök áttekintése](iot-hub-devguide-device-twins.md)
    * [Eszköz-ikertulajdonságok használata](tutorial-device-twins.md)
    * [Ajánlott eljárások az IoT-megoldás belüli eszközök konfigurálása](iot-hub-configuration-best-practices.md)

* **Üzembe helyezés**: Lehetővé teszi az új IoT Hub-eszközök biztonságos üzembe helyezését, valamint a kezelők számára az eszközök képességeinek azonnali elsajátítását.  Az IoT Hub-identitásjegyzék alkalmazásával rugalmas eszközidentitások és -hitelesítő adatok hozhatók létre, illetve egy feladat használatával ez a művelet tömegesen is végrehajtható. Az eszközök felépítését úgy végezze el, hogy azok képességei és állapotai az ikereszköz tulajdonságaiban nyomon követhetőek legyenek.
  
    *További információk*: 
    * [Eszközidentitások kezelése](iot-hub-devguide-identity-registry.md)
    * [Eszközidentitások tömeges kezelése](iot-hub-bulk-identity-mgmt.md)
    * [Eszköz-ikertulajdonságok használata](tutorial-device-twins.md)
    * [Ajánlott eljárások az IoT-megoldás belüli eszközök konfigurálása](iot-hub-configuration-best-practices.md)
    * [Azure IoT Hub Device Provisioning Service](https://azure.microsoft.com/documentation/services/iot-dps)

* **Konfigurálás**: az eszközök kötegelt konfigurációmódosításainak és belső vezérlőprogramjuk frissítéseinek megkönnyítése üzemi állapotuk és biztonságuk megőrzésével. A kívánt tulajdonságok, illetve közvetlen módszerek és szórásos feladatok használatával ezek az eszközfelügyeleti műveletek tömegesen is végrehajthatók.
  
    *További információk*:
    * [Eszköz-ikertulajdonságok használata](tutorial-device-twins.md)
    * [Konfigurálhatja és figyelheti a nagy mennyiségű IoT-eszközök](iot-hub-auto-device-config.md)
    * [Ajánlott eljárások az IoT-megoldás belüli eszközök konfigurálása](iot-hub-configuration-best-practices.md)

* **Figyelés**: A teljes eszközgyűjtemény és a folyamatban lévő műveletek állapotának figyelése, valamint a kezelők riasztása az esetlegesen figyelmet érdemlő problémák kapcsán.  Az ikereszközök alkalmazásával az eszközök képesek a valós idejű működési feltételek és a frissítési műveletek állapotának jelentésére. Nagyteljesítményű irányítópulti jelentések hozhatók létre, amelyek az ikereszköz-lekérdezések használatával felszínre hozzák az azonnali intézkedést igénylő problémákat.
  
    *További információk*: 
    * [Eszköz-ikertulajdonságok használata](tutorial-device-twins.md)
    * [Az IoT Hub lekérdezési nyelv az ikereszközökhöz, feladatokkal és üzenet-útválasztása](iot-hub-devguide-query-language.md)
    * [Konfigurálhatja és figyelheti a nagy mennyiségű IoT-eszközök](iot-hub-auto-device-config.md)
    * [Ajánlott eljárások az IoT-megoldás belüli eszközök konfigurálása](iot-hub-configuration-best-practices.md)

* **Kivonás**: cseréje vagy eszközök leszerelése meghibásodás után, vagy frissítési ciklus, vagy a szolgáltatás élettartamának végén.  Az ikereszközök használatával az eszközinformációk abban az esetben is megőrizhetők, ha a fizikai eszközt kicserélik vagy a kivonás során archiválják. Az IoT Hub-identitásjegyzék alkalmazásával lehetséges az eszközidentitások és -hitelesítő adatok biztonságos visszahívása.
  
    *További információk*: 
    * [Eszköz-ikertulajdonságok használata](tutorial-device-twins.md)
    * [Eszközidentitások kezelése](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>Eszközfelügyeleti minták

Az IoT Hub az alábbi eszközfelügyeleti mintákat engedélyezi. A [eszközfelügyeleti oktatóanyagok](iot-hub-node-node-device-management-get-started.md) illeszkedjenek a pontos forgatókönyvhöz e minták kiterjesztését, és hogyan alapminták alapján új mintákat tervezhet bemutatják, részletesebben.

* **Újraindítás**: A háttéralkalmazás az eszköz metódus segítségével tájékoztatja a közvetlen, hogy kezdeményezte az újraindítást.  Az eszköz a jelentett tulajdonságokat felhasználva frissíti az eszköz újraindítási állapotát.
  
    ![Az eszközfelügyelet újraindítási mintájának ábrája](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **Gyári beállítások visszaállítása**: A háttéralkalmazás az eszköz metódus segítségével tájékoztatja a közvetlen, hogy kezdeményezte a gyári beállítások visszaállítását. Az eszköz a jelentett tulajdonságokat felhasználva frissíti az eszköz gyári visszaállítási állapotát.
  
    ![Az eszközfelügyelet eszköz gyári alaphelyzetbe állítási mintájának ábrája](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **Konfigurációs**: A háttéralkalmazás kívánt tulajdonságait felhasználva konfigurálja az eszközön futó szoftvert. Az eszköz a jelentett tulajdonságokat felhasználva frissíti az eszköz konfigurálási állapotát.
  
    ![Az eszközfelügyelet konfigurációs mintájának ábrája](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **Belső vezérlőprogram frissítésének**: A háttéralkalmazás egy automatikus Eszközkezelési konfiguráció segítségével válassza ki az eszközöket fogadja a frissítést, hogy az eszközök helyét keresse meg a frissítést, és a frissítési folyamat figyelésére. Az eszköz többlépéses folyamatot kezdeményez, tölthet le, győződjön meg arról, és alkalmazza a belsővezérlőprogram-lemezképet, és majd indítsa újra az eszközt az IoT Hub szolgáltatást újracsatlakozás előtt. A több lépésből álló folyamat során az eszköz a jelentett tulajdonságokat felhasználva frissíti az eszköz előrehaladását és állapotát.
  
    ![Az eszközfelügyelet belsővezérlőprogram-frissítési mintájának ábrája](media/iot-hub-device-management-overview/fwupdate-pattern.png)

* **Előrehaladási és állapotjelentések**: A megoldás háttérrendszere lefuttatja az ikereszköz lekérdezéseit, az eszközök állapotának és az eszközökön futó műveletek állapotának jelentésére között.
  
    ![Az eszközfelügyelet előrehaladási és állapotmeghatározási jelentéskészítési mintájának ábrája](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="next-steps"></a>További lépések

Az IoT Hub eszközfelügyeleti szolgáltatásai által biztosított képességek, minták és kódtárak használatával olyan IoT-alkalmazások hozhatók létre, amelyek az eszköz teljes életciklusában képesek megfelelni a vállalati IoT-kezelők követelményeinek.

Szeretne részletesebben is megismerkedni az eszközfelügyeleti szolgáltatásaival az IoT Hub, tekintse meg a [Eszközfelügyelet – első lépések](iot-hub-node-node-device-management-get-started.md) oktatóanyag.