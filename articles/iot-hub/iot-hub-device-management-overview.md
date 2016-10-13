<properties
 pageTitle="Az eszközfelügyelet áttekintése | Microsoft Azure"
 description="Az Azure IoT Hub-eszközfelügyelet áttekintése"
 services="iot-hub"
 documentationCenter=""
 authors="bzurcher"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/16/2016"
 ms.author="bzurcher"/>




# Az Azure IoT Hub-eszközfelügyelet áttekintése (előzetes verzió)

## Az Azure IoT-eszközfelügyeleti módszer

Az Azure IoT Hub-eszközfelügyelet biztosítja a funkciókat és a bővíthetőségi modellt az eszközök és háttéralkalmazások számára annak érdekében, hogy az IoT-eszközfelügyeletet felhasználhassák az IoT-ben szereplő különféle eszközökhöz és protokollokhoz.  Az IoT-ben különböző eszközök találhatók a nagy mértékben korlátozott érzékelőktől és egyetlen célra használható mikrovezérlőktől kezdve a nagyobb teljesítményű átjárókig, amelyek más eszközöket és protokollokat engedélyeznek.  Az IoT-megoldások jelentősen eltérnek a függőleges tartományokban és az olyan alkalmazásokban, amelyeknél minden egyes tartomány kezelőihez egyedi használati esetek tartoznak.  Az IoT-megoldások az IoT-eszközfelügyelet képességeit, mintáit és kódtárait felhasználva engedélyezhetik a különféle eszközöket és felhasználókat tartalmazó állományok eszközfelügyeletét.  

## Bevezetés

Egy sikeres IoT-megoldás létrehozásának fontos részét képezi egy stratégia biztosítása arra vonatkozóan, hogyan végezzék a kezelők az eszközflotta folyamatos felügyeletét. Az IoT-kezelőknek olyan egyszerű és megbízható eszközökre és alkalmazásokra van szükségük, amelyek segítségével feladataik stratégiailag fontosabb szempontjaira koncentrálhatnak. Azure IoT Hub olyan építőelemeket biztosít az IoT-alkalmazások létrehozásához, amelyek megkönnyítik a legfontosabb eszközfelügyeleti minták létrehozását.

Az eszközök akkor igénylik az IoT Hub-os felügyeletet, ha olyan, eszközfelügyeleti ügynöknek nevezett egyszerű alkalmazást futtatnak, amely az eszközt biztonságosan csatlakoztatja a felhőhöz. Az ügynökkód lehetővé teszi, hogy az alkalmazási oldalról egy kezelő távolról hitelesítse az eszköz állapotát, és elvégezze az olyan felügyeleti műveleteket, mint például a hálózat konfigurációs módosításainak alkalmazása vagy belső vezérlőprogram frissítéseinek telepítése.

## Az IoT-eszközfelügyelet alapelvei

Az IoT a felügyeleti kihívások egyedi készletét hordozza magában, és olyan megoldást, amely a IoT-eszközfelügyeleti alapelveket biztosítja:

![][img-dm_principles]

- **Méretezés és automatizálás**: az IoT olyan egyszerű eszközöket igényel, amelyekkel automatizálhatók a rutinfeladatok, és aránylag kis üzemeltetői létszámmal az eszközök millióinak felügyeletét biztosítják. A kezelők napról napra azt várják, hogy távolról, kötegelten kezelhessék az eszközök működését, és a rendszer csak akkor riassza őket, ha olyan hiba történt, amelynek elhárításához a közvetlen beavatkozásukra van szükség.

- **Nyitottság és kompatibilitás**: az IoT-eszközök ökoszisztémája rendkívül szerteágazó. A felügyeleti eszközöket úgy kell kialakítani, hogy alkalmasak legyenek nagy mennyiségű eszközosztály, platform és protokoll befogadására. A kezelők képesek legyenek az összes eszköz támogatására, a legnagyobb mértékben korlátozott, beágyazott, egyetlen folyamatot végző processzorlapkáktól a nagy teljesítményű és teljes funkcionalitású számítógépekig.

- **A környezet figyelése**: az IoT-környezetek dinamikusak és állandóan változnak. A szolgáltatás megbízhatósága rendkívül fontos. Az eszközfelügyeleti műveleteknek szerepelniük kell az SLA karbantartási ablakaiban, a hálózat és a tápellátás állapotában, a használatban lévő feltételekben és az eszköz földrajzi helyeiben annak biztosításához, hogy a karbantartási állásidő ne befolyásolja a létfontosságú üzleti tevékenységeket, illetve ne idézzen elő veszélyes feltételeket.

- **Sok szerepkör kiszolgálása**: az IoT-műveletekhez tartozó szerepkörök egyedi munkafolyamatainak és folyamatainak támogatása rendkívül fontos. Az üzemeltető személyzetnek harmonikusan együtt kell működnie a belső informatikai részlegek adott korlátozásaival, és biztosítania kell az eszközműveletekre vonatkozó információkat a felügyelők és más vezetői szerepkörök számára.

## Az IoT-eszközök életciklusa 

Bár az IoT-projektek nagy mértékben eltérnek egymástól, számos közös minta létezik az eszközök felügyeletéhez. Az Azure IoT-ban ezeket a mintákat a rendszer az IoT-eszköz életciklusában azonosítja, amely az alábbi öt különböző szakaszból áll:

![][img-device_lifecycle]

1. **Tervezés**: a kezelők olyan eszköztulajdonság-sémát hozhatnak létre, amely segítségével könnyen és pontosan lekérdezhetnek és megcélozhatnak egy eszközcsoportot, amelyen kötegelt felügyeleti műveleteket hajthatnak végre.

    *Kapcsolódó építőelemek*: [Bevezetés az ikereszközök használatába][lnk-twins-getstarted], [Az ikertulajdonságok használata][lnk-twin-properties]

2. **Kiépítés**: az új eszközök biztonságos hitelesítése az IoT Hub számára, és lehetőség biztosítása a kezelők számára az eszköz képességeinek és aktuális állapotának azonnali felderítésére.

    *Kapcsolódó építőelemek*: [Bevezetés az IoT Hub használatába][lnk-hub-getstarted], [Az ikertulajdonságok használata][lnk-twin-properties]

3. **Konfigurálás**: az eszközök kötegelt konfigurációmódosításainak és belső vezérlőprogramjuk frissítéseinek megkönnyítése üzemi állapotuk és biztonságuk megőrzésével.

    *Kapcsolódó építőelemek*: [Az ikertulajdonságok használata][lnk-twin-properties], [C2D metódusok][lnk-c2d-methods], [Feladatok ütemezése/szórása][lnk-jobs]

4. **Figyelés**: eszközflotta általános üzemi állapotának és a folyamatban lévő frissítéskibocsátások állapotának figyelése annak érdekében, hogy riaszthassák a kezelőket a beavatkozásukat igénylő problémák felmerülésével kapcsolatban.

    *Kapcsolódó építőelemek *: [Az ikertulajdonságok használata][lnk-twin-properties]

5. **Kivonás**: az eszközök lecserélése vagy leszerelése meghibásodás vagy frissítési ciklus után, illetve a szolgáltatás élettartamának végén.

    *Kapcsolódó építőelemek *:
    
## IoT Hub-eszközfelügyeleti minták

Az IoT Hub a (kezdeti) eszközfelügyeleti minták következő csoportját biztosítja.  Ahogy az [oktatóprogramok][lnk-get-started] bemutatják, ezeket a mintákat úgy bővítheti, hogy illeszkedjenek a pontos forgatókönyvhöz, és ezen alapminták alapján új mintákat tervezhet más forgatókönyvekhez.

1. **Újraindítás** – A háttéralkalmazás D2C metódus segítségével tájékoztatja az eszközt az újraindítás kezdeményezéséről.  Az eszköz az ikereszköz jelentett tulajdonságait felhasználva frissíti az eszköz újraindítási állapotát. 

    ![][img-reboot_pattern]

2. **Gyári visszaállítás** – A háttéralkalmazás egy D2C metódus segítségével tájékoztatja az eszközt egy gyári visszaállítás kezdeményezéséről.  Az eszköz az ikereszköz jelentett tulajdonságait felhasználva frissíti az eszköz gyári visszaállítási állapotát.

    ![][img-facreset_pattern]

3. **Konfigurálás** – A háttéralkalmazás az ikereszköz kívánt tulajdonságait felhasználva konfigurálja az eszközön futó szoftvert.  Az eszköz az ikereszköz jelentett tulajdonságait felhasználva frissíti az eszköz konfigurálási állapotát. 

    ![][img-config_pattern]

4. **Belső vezérlőprogram frissítése** – A háttéralkalmazás egy D2C metódus segítségével tájékoztatja az eszközt a belső vezérlőprogram frissítésének kezdeményezéséről.  Az eszköz egy több lépésből álló folyamatot kezdeményez a belsővezérlőprogram-csomag letöltésére, alkalmazza a belsővezérlőprogram-csomagot, végül újból csatlakozik az IoT Hub szolgáltatáshoz.  A több lépésből álló folyamat során az eszköz az ikereszköz jelentett tulajdonságait felhasználva frissíti az eszköz előrehaladási folyamatát és az állapotát. 

    ![][img-fwupdate_pattern]

5. **Előrehaladási folyamat és állapot jelentése** – A háttéralkalmazás lefuttatja az ikereszköz lekérdezéseit egy eszközcsoporton, és jelentést készít az eszközön futó műveletek előrehaladási folyamatáról és állapotáról.

    ![][img-report_progress_pattern]

## Következő lépések

Az Azure IoT Hub által biztosított építőelemek segítségével a fejlesztők olyan IoT-alkalmazásokat hozhatnak létre, amelyek az eszköz életciklusának minden egyes szakaszában megfelelnek az IoT-kezelő egyedi követelményeinek.

Ha szeretne részletesebben is megismerkedni az Azure IoT Hub eszközfelügyeleti funkcióival, olvassa el a [Get started with Azure IoT Hub device management][lnk-get-started] (Ismerkedés az Azure IoT Hub eszközfelügyeleti funkcióival) című oktatóanyagot.

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-jobs]: iot-hub-schedule-jobs.md


<!--HONumber=Oct16_HO1-->


