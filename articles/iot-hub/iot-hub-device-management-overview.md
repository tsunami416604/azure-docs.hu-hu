---
title: Az IoT Hub-eszközfelügyelet – Áttekintés | Microsoft Docs
description: 'Ez a cikk az Azure IoT Hub-eszközfelügyelet következő témáiról ad áttekintést: vállalati eszközök életciklusa, újraindítás, gyári beállítások visszaállítása, belső vezérlőprogram frissítése, konfigurálás, ikereszközök, lekérdezések, feladatok'
services: iot-hub
documentationcenter: ''
author: bzurcher
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: bzurcher

---
# <a name="overview-of-azure-iot-hub-device-management-(preview)"></a>Az Azure IoT Hub-eszközfelügyelet áttekintése (előzetes verzió)
## <a name="introduction"></a>Bevezetés
Az Azure IoT Hub által biztosított szolgáltatások és bővíthetőségi modell segítségével az eszköz- és háttérrendszer-fejlesztők hatékony IoT eszközfelügyeleti megoldásokat építhetnek ki. Az IoT-eszközök köre a korlátozott érzékelőktől és az egycélú mikrovezérlőktől az eszközcsoportok kommunikációjának továbbítását végző, nagyteljesítményű átjárókig terjed.  Emellett az IoT-műveletek használati módja és követelményei az egyes iparágak esetében jelentősen eltér.  Ezen eltérések ellenére az Azure IoT Hub-eszközfelügyelet az eszközök és végfelhasználók változatos együttesének igényeihez igazítva biztosítja a szükséges képességeket, mintákat és kódelemtárakat.

A vállalati IoT-megoldások sikeres létrehozásának fontos része annak a stratégiának a megalkotása, amely a kezelők számára a folyamatos eszközfelügyelet módját ismerteti. Az IoT-kezelőknek olyan egyszerű és megbízható eszközökre és alkalmazásokra van szükségük, amelyek segítségével a feladataik stratégiai szempontjaira koncentrálhatnak. Ez a cikk a következő információkat tartalmazza:

* Az Azure IoT Hub-eszközfelügyelet rövid áttekintése.
* Az általános eszközfelügyeleti alapelvek leírása.
* Az eszközök életciklusának ismertetése.
* Az általános eszközfelügyeleti minták áttekintése.

## <a name="iot-device-management-principles"></a>Az IoT-eszközfelügyelet alapelvei
Az IoT alkalmazása egyedi eszközfelügyeleti kihívásokat hoz magával, a következő alapelveket pedig a vállalati szintű megoldások mindegyikének magában kell foglalnia:

![Az Azure IoT Hub-eszközfelügyelet alapelveinek ábrája][img-dm_principles]

* **Méretezés és automatizálás**: Az IoT-megoldások olyan egyszerű eszközök alkalmazását igénylik, amelyek képesek a rutinfeladatok automatizálására, valamint amelyekkel eszközök millióinak kezelése oldható meg viszonylag kis kezelői létszámmal. A kezelők napról napra azt várják, hogy távolról, kötegelten kezelhessék az eszközök működését, és a rendszer csak akkor riassza őket, ha olyan hiba történt, amelynek elhárításához a közvetlen beavatkozásukra van szükség.
* **Nyitottság és kompatibilitás**: az IoT-eszközök ökoszisztémája rendkívül szerteágazó. A felügyeleti eszközöket úgy kell kialakítani, hogy alkalmasak legyenek nagy mennyiségű eszközosztály, platform és protokoll befogadására. A kezelőknek képesnek kell lenniük számos eszköztípus kezelésére, a legkorlátozottabb, beágyazott egyutas lapkáktól kezdve a nagyteljesítményű, teljeskörű funkciókkal ellátott számítógépekig.
* **A környezet figyelése**: az IoT-környezetek dinamikusak és állandóan változnak. A szolgáltatás megbízhatósága létfontosságú. Az eszközfelügyeleti műveleteknek szerepelniük kell az SLA karbantartási ablakaiban, a hálózat és a tápellátás állapotában, a használatban lévő feltételekben és az eszköz földrajzi helyeiben annak biztosításához, hogy a karbantartási állásidő ne befolyásolja a létfontosságú üzleti tevékenységeket, illetve ne idézzen elő veszélyes feltételeket.
* **Sok szerepkör kiszolgálása**: az IoT-műveletekhez tartozó szerepkörök egyedi munkafolyamatainak és folyamatainak támogatása rendkívül fontos. Az üzemeltetést végző stábnak zökkenőmentesen kell együttműködnie a belső informatikai részlegek által meghatározott korlátozásokkal.  Fenntartható módot kell találniuk továbbá a valós idejű eszközművelet-információk beillesztésére a felettesi és üzletfelügyeleti szerepkörökbe.

## <a name="iot-device-lifecycle"></a>Az IoT-eszközök életciklusa
Egyes általános eszközfelügyeleti szakaszok minden vállalati IoT-projektben megjelennek. Az Azure IoT-ben az IoT-eszközök életciklusának öt szakaszát különböztetjük meg:

![Az Azure IoT-eszközök életciklusának öt fázisa: tervezés, üzembe helyezés, konfigurálás, figyelés, kivonás][img-device_lifecycle]

A teljes megoldás megvalósításához számos eszközkezelői követelménynek kell teljesülnie ezen az öt szakaszon belül:

* **Tervezés**: Lehetővé teszi a kezelők számára olyan eszközmetaadat-séma létrehozását, amellyel egyszerű és pontos lekérdezés végezhető el egy adott eszközcsoporton a tömeges felügyeleteken. Az ikereszközök használatával e metaadatokat címkék és tulajdonságok formájában tárolhatja.
  
    *További információk*: [Kezdeti lépések az ikereszközökhöz][lnk-twins-getstarted], [Az ikereszközök megértése][lnk-twins-devguide], [Az ikertulajdonságok használata][lnk-twin-properties]
* **Üzembe helyezés**: Lehetővé teszi az új IoT Hub-eszközök biztonságos üzembe helyezését, valamint a kezelők számára az eszközök képességeinek azonnali elsajátítását.  Az IoT-eszközjegyzék alkalmazásával rugalmas eszközidentitások és -hitelesítő adatok hozhatók létre, illetve egy feladat használatával ez a művelet tömegesen is végrehajtható. Az eszközök felépítését úgy végezze el, hogy azok képességei és állapotai az ikereszköz tulajdonságaiban nyomon követhetőek legyenek.
  
    *További információk*: [Eszközidentitások kezelése][lnk-identity-registry], [Az eszközidentitások tömeges kezelése][lnk-bulk-identity], [Az ikertulajdonságok használata][lnk-twin-properties]
* **Konfigurálás**: az eszközök kötegelt konfigurációmódosításainak és belső vezérlőprogramjuk frissítéseinek megkönnyítése üzemi állapotuk és biztonságuk megőrzésével. A kívánt tulajdonságok, illetve közvetlen módszerek és szórásos feladatok használatával ezek az eszközfelügyeleti műveletek tömegesen is végrehajthatók.
  
    *További információk*: [Közvetlen módszerek használata][lnk-c2d-methods], [Közvetlen módszer meghívása egy eszközön][lnk-methods-devguide], [Az ikertulajdonságok használata][lnk-twin-properties], [Feladatok ütemezése és szórása][lnk-jobs], [Feladatok ütemezése több eszközön][lnk-jobs-devguide]
* **Figyelés**: A teljes eszközgyűjtemény és a folyamatban lévő műveletek állapotának figyelése, valamint a kezelők riasztása az esetlegesen figyelmet érdemlő problémák kapcsán.  Az ikereszközök alkalmazásával az eszközök képesek a valós idejű működési feltételek és a frissítési műveletek állapotának jelentésére. Nagyteljesítményű irányítópulti jelentések hozhatók létre, amelyek az ikereszköz-lekérdezések használatával felszínre hozzák az azonnali intézkedést igénylő problémákat.
  
    *További információk*: [Az ikertulajdonságok használata][lnk-twin-properties], [Nyelv lekérdezése az ikrekhez és a feladatokhoz][lnk-query-language]
* **Kivonás**: az eszközök lecserélése vagy leszerelése meghibásodás vagy frissítési ciklus után, illetve a szolgáltatás élettartamának végén.  Az ikereszközök használatával az eszközinformációk abban az esetben is megőrizhetők, ha a fizikai eszközt kicserélik vagy a kivonás során archiválják. Az IoT-eszközjegyzék alkalmazásával lehetséges az eszközidentitások és -hitelesítő adatok biztonságos visszahívása.
  
    *További információk*: [Az ikertulajdonságok használata][lnk-twin-properties], [Az eszközidentitások kezelése][lnk-identity-registry]

## <a name="iot-hub-device-management-patterns"></a>IoT Hub-eszközfelügyeleti minták
Az IoT Hub az alábbi eszközfelügyeleti mintákat engedélyezi.  Az [eszközfelügyeleti oktatóanyagok][lnk-get-started] részletesebben is bemutatják e minták kiterjesztését valós helyzetekre, illetve új minták létrehozását az alapminták alapján.

* **Újraindítás** – A háttéralkalmazás közvetlen módszerrel értesíti az eszközt arról, hogy kezdeményezte az újraindítást.  Az eszköz az ikereszköz jelentett tulajdonságait felhasználva frissíti az eszköz újraindítási állapotát.
  
    ![Az Azure IoT Hub-eszközfelügyelet újraindítási mintájának ábrája][img-reboot_pattern]
* **Gyári beállítások visszaállítása** – A háttéralkalmazás közvetlen módszerrel értesíti az eszközt arról, hogy kezdeményezte a gyári beállítások visszaállítását.  Az eszköz az ikereszköz jelentett tulajdonságait felhasználva frissíti az eszköz gyári visszaállítási állapotát.
  
    ![Az Azure IoT Hub-eszközfelügyelet eszköz gyári alaphelyzetbe állítási mintájának ábrája][img-facreset_pattern]
* **Konfigurálás** – A háttéralkalmazás az ikereszköz kívánt tulajdonságait felhasználva konfigurálja az eszközön futó szoftvert.  Az eszköz az ikereszköz jelentett tulajdonságait felhasználva frissíti az eszköz konfigurálási állapotát.
  
    ![Az Azure IoT Hub-eszközfelügyelet konfigurációs mintájának ábrája][img-config_pattern]
* **Belső vezérlőprogram frissítése** – A háttéralkalmazás közvetlen módszerrel értesíti az eszközt arról, hogy kezdeményezte a belső vezérlőprogram frissítését.  Az eszköz többlépéses folyamatot kezdeményez, amelynek keretében megtörténik a belsővezérlőprogram-csomag letöltése és alkalmazása, végül pedig az IoT Hub-szolgáltatáshoz történő újracsatlakozás.  A több lépésből álló folyamat során az eszköz az ikereszköz jelentett tulajdonságait felhasználva frissíti az eszköz előrehaladási folyamatát és az állapotát.
  
    ![Az Azure IoT Hub-eszközfelügyelet belsővezérlőprogram-frissítési mintájának ábrája][img-fwupdate_pattern]
* **Előrehaladási és állapotjelentések** – Az alkalmazás a háttérben ikereszköz-lekérdezéseket futtat több eszközön, így képes az eszközökön futó műveletek állapotának és előrehaladásának jelentésére.
  
    ![Az Azure IoT Hub-eszközfelügyelet előrehaladási és állapotmeghatározási jelentéskészítési mintájának ábrája][img-report_progress_pattern]

## <a name="next-steps"></a>Következő lépések
Az Azure IoT Hub eszközfelügyeleti szolgáltatásai által biztosított képességek, minták és kódelemtárak használatával olyan IoT-alkalmazások hozhatók létre, amelyek az eszköz teljes életciklusában képesek megfelelni a vállalati IoT-kezelők követelményeinek.

Ha szeretne részletesebben is megismerkedni az Azure IoT Hub eszközfelügyeleti funkcióival, olvassa el a [Get started with Azure IoT Hub device management][lnk-get-started] (Ismerkedés az Azure IoT Hub eszközfelügyeleti funkcióival) című oktatóanyagot.

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-twins-devguide]: iot-hub-devguide-device-twins.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-query-language]: iot-hub-devguide-query-language
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-methods-devguide]: iot-hub-devguide-direct-methods.md
[lnk-jobs]: iot-hub-schedule-jobs.md
[lnk-jobs-devguide]: iot-hub-devguide-jobs.md


<!--HONumber=Oct16_HO3-->


