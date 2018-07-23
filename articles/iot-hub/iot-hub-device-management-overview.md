---
title: Eszközkezelés az Azure IoT Hub használatával | Microsoft Docs
description: 'Az Azure IoT Hub eszközfelügyeleti lehetőségeinek áttekintése: vállalati eszközök életciklusa és eszközfelügyeleti minták, mint például újraindítás, gyári beállítások visszaállítása, belső vezérlőprogram frissítése, konfigurálás, ikereszközök, lekérdezések, feladatok.'
author: bzurcher
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: briz
ms.openlocfilehash: 55c28b9ec39ffd617c816d76f67ff6f9853d7012
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185914"
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

![Az eszközfelügyelet alapelveinek ábrája][img-dm_principles]

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

![Az Azure IoT-eszközök életciklusának öt fázisa: tervezés, üzembe helyezés, konfigurálás, figyelés, kivonás][img-device_lifecycle]

A teljes megoldás megvalósításához számos eszközkezelői követelménynek kell teljesülnie ezen az öt szakaszon belül:

* **Tervezés**: Lehetővé teszi a kezelők számára olyan eszközmetaadat-séma létrehozását, amellyel egyszerű és pontos lekérdezés végezhető el egy adott eszközcsoporton a tömeges felügyeleteken. Az ikereszközök használatával e metaadatokat címkék és tulajdonságok formájában tárolhatja.
  
    *További információk*: [ikereszközök – első lépések][lnk-twins-getstarted], [Ikereszközök][lnk-twins-devguide], [hogyan eszköz-ikertulajdonságok használata][lnk-twin-properties], [ajánlott eljárások az IoT-megoldás belül eszközkonfiguráció][lnk-adm-best-practices].
* **Üzembe helyezés**: Lehetővé teszi az új IoT Hub-eszközök biztonságos üzembe helyezését, valamint a kezelők számára az eszközök képességeinek azonnali elsajátítását.  Az IoT Hub-identitásjegyzék alkalmazásával rugalmas eszközidentitások és -hitelesítő adatok hozhatók létre, illetve egy feladat használatával ez a művelet tömegesen is végrehajtható. Az eszközök felépítését úgy végezze el, hogy azok képességei és állapotai az ikereszköz tulajdonságaiban nyomon követhetőek legyenek.
  
    *További információk*: [eszközidentitások kezelése][lnk-identity-registry], [tömeges eszközidentitások kezelése][lnk-bulk-identity], [Eszköz-ikertulajdonságok használata][lnk-twin-properties], [ajánlott eljárások az IoT-megoldás belül eszközkonfiguráció][lnk-adm-best-practices], [Az azure IoT Hub Device Provisioning Service][lnk-dps].
* **Konfigurálás**: az eszközök kötegelt konfigurációmódosításainak és belső vezérlőprogramjuk frissítéseinek megkönnyítése üzemi állapotuk és biztonságuk megőrzésével. A kívánt tulajdonságok, illetve közvetlen módszerek és szórásos feladatok használatával ezek az eszközfelügyeleti műveletek tömegesen is végrehajthatók.
  
    *További információk*: [eszköz-ikertulajdonságok használata][lnk-twin-properties], [konfigurálása és figyelése a nagy mennyiségű IoT-eszközök][lnk-adm-how-to], [ Ajánlott eljárások az IoT-megoldás belül eszközkonfiguráció][lnk-adm-best-practices].
* **Figyelés**: A teljes eszközgyűjtemény és a folyamatban lévő műveletek állapotának figyelése, valamint a kezelők riasztása az esetlegesen figyelmet érdemlő problémák kapcsán.  Az ikereszközök alkalmazásával az eszközök képesek a valós idejű működési feltételek és a frissítési műveletek állapotának jelentésére. Nagyteljesítményű irányítópulti jelentések hozhatók létre, amelyek az ikereszköz-lekérdezések használatával felszínre hozzák az azonnali intézkedést igénylő problémákat.
  
    *További információk*: [eszköz-ikertulajdonságok használata][lnk-twin-properties], [az IoT Hub lekérdezési nyelv az ikereszközökhöz, feladatokkal és üzenet-útválasztása] [ lnk-query-language], [Konfigurálása és figyelése a nagy mennyiségű IoT-eszközök][lnk-adm-how-to], [ajánlott eljárások az IoT-megoldás belül eszközkonfiguráció] [lnk-adm-best-practices].
* **Kivonás**: az eszközök lecserélése vagy leszerelése meghibásodás vagy frissítési ciklus után, illetve a szolgáltatás élettartamának végén.  Az ikereszközök használatával az eszközinformációk abban az esetben is megőrizhetők, ha a fizikai eszközt kicserélik vagy a kivonás során archiválják. Az IoT Hub-identitásjegyzék alkalmazásával lehetséges az eszközidentitások és -hitelesítő adatok biztonságos visszahívása.
  
    *További információk*: [Az ikertulajdonságok használata][lnk-twin-properties], [Eszközidentitások kezelése][lnk-identity-registry]

## <a name="device-management-patterns"></a>Eszközfelügyeleti minták
Az IoT Hub az alábbi eszközfelügyeleti mintákat engedélyezi.  Az [eszközfelügyeleti oktatóanyagok][lnk-get-started] részletesebben is bemutatják e minták kiterjesztését valós helyzetekre, illetve új minták létrehozását az alapminták alapján.

* **Újraindítás** – A háttéralkalmazás közvetlen módszerrel értesíti az eszközt arról, hogy kezdeményezte az újraindítást.  Az eszköz a jelentett tulajdonságokat felhasználva frissíti az eszköz újraindítási állapotát.
  
    ![Az eszközfelügyelet újraindítási mintájának ábrája][img-reboot_pattern]
* **Gyári beállítások visszaállítása** – A háttéralkalmazás közvetlen módszerrel értesíti az eszközt arról, hogy kezdeményezte a gyári beállítások visszaállítását.  Az eszköz a jelentett tulajdonságokat felhasználva frissíti az eszköz gyári visszaállítási állapotát.
  
    ![Az eszközfelügyelet eszköz gyári alaphelyzetbe állítási mintájának ábrája][img-facreset_pattern]
* **Konfigurálás** – A háttéralkalmazás az eszköz kívánt tulajdonságait felhasználva konfigurálja az eszközön futó szoftvert.  Az eszköz a jelentett tulajdonságokat felhasználva frissíti az eszköz konfigurálási állapotát.
  
    ![Az eszközfelügyelet konfigurációs mintájának ábrája][img-config_pattern]
* **Belső vezérlőprogram frissítésének** – a háttéralkalmazás egy automatikus Eszközkezelési konfiguráció segítségével válassza ki az eszközöket fogadja a frissítést, hogy az eszközök helyét keresse meg a frissítést, és a frissítési folyamat figyelésére. Az eszköz többlépéses folyamatot kezdeményez, tölthet le, győződjön meg arról, és alkalmazza a belsővezérlőprogram-lemezképet, és majd indítsa újra az eszközt az IoT Hub szolgáltatást újracsatlakozás előtt.  A több lépésből álló folyamat során az eszköz a jelentett tulajdonságokat felhasználva frissíti az eszköz előrehaladását és állapotát.
  
    ![Az eszközfelügyelet belsővezérlőprogram-frissítési mintájának ábrája][img-fwupdate_pattern]
* **Előrehaladási és állapotjelentések** – A megoldás a háttérben ikereszköz-lekérdezéseket futtat több eszközön, így képes az eszközökön futó műveletek állapotának és előrehaladásának jelentésére.
  
    ![Az eszközfelügyelet előrehaladási és állapotmeghatározási jelentéskészítési mintájának ábrája][img-report_progress_pattern]

## <a name="next-steps"></a>További lépések
Az IoT Hub eszközfelügyeleti szolgáltatásai által biztosított képességek, minták és kódtárak használatával olyan IoT-alkalmazások hozhatók létre, amelyek az eszköz teljes életciklusában képesek megfelelni a vállalati IoT-kezelők követelményeinek.

Ha szeretne részletesebben is megismerkedni az IoT Hub eszközfelügyeleti funkcióival, tekintse meg az [eszközfelügyelet első lépéseit][lnk-get-started] bemutató oktatóanyagot.

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-twins-devguide]: iot-hub-devguide-device-twins.md
[lnk-get-started]: iot-hub-node-node-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: tutorial-device-twins.md
[lnk-hub-getstarted]: quickstart-send-telemetry-dotnet.md
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-query-language]: iot-hub-devguide-query-language.md
[lnk-c2d-methods]: quickstart-control-device-node.md
[lnk-methods-devguide]: iot-hub-devguide-direct-methods.md
[lnk-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-jobs-devguide]: iot-hub-devguide-jobs.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
[lnk-adm-best-practices]: iot-hub-configuration-best-practices.md
[lnk-adm-how-to]: iot-hub-auto-device-config.md