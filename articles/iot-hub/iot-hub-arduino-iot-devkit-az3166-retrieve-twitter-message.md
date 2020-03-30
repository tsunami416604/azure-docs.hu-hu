---
title: Twitter-üzenet lekérése az Azure Functions szolgáltatással | Microsoft dokumentumok
description: A mozgásérzékelő vel észlelheti a remegést, és az Azure Functions segítségével véletlenszerű enyveskezhet egy általa megadott hashtaggel
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: dc4ff35ff04680e8635d54c25212c8ae639ae472
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60779835"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Shake, Shake egy Tweet - Lekérés egy Twitter üzenetet az Azure Functions

Ebben a projektben megtudhatja, hogyan használhatja a mozgásérzékelőt egy esemény aktiválásához az Azure Functions használatával. Az alkalmazás letölti a véletlenszerű csipog egy #hashtag konfigurálja a Arduino vázlatot. A tweet megjelenik a DevKit képernyőn.

## <a name="what-you-need"></a>Mi szükséges

Az [első lépések útmutatójának](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) befejezése:

* A DevKit wi-fi hálózathoz csatlakoztatva.
* Készítse elő a fejlesztői környezetet.

Aktív Azure-előfizetés. Ha még nem rendelkezik ilyen, az alábbi módszerek egyikével regisztrálhat:

* [Ingyenes, 30 napos próbaverziós Microsoft Azure-fiók](https://azure.microsoft.com/free/) aktiválása
* [Igényelje Azure-kreditjét,](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ha Ön MSDN- vagy Visual Studio-előfizető

## <a name="open-the-project-folder"></a>A projektmappa megnyitása

Először nyissa meg a projektmappát. 

### <a name="start-vs-code"></a>Vs-kód indítása

* Győződjön meg arról, hogy a DevKit csatlakozik a számítógéphez.

* Indítsa el a VS Code-ot.

* Csatlakoztassa a DevKit-et a számítógéphez.

   > [!NOTE]
   > A VS Code indításakor hibaüzenet jelenhet meg, hogy az Arduino IDE vagy a kapcsolódó táblacsomag nem található. Ha ez a hiba jelentkezik, zárja be a VS Code-ot, és indítsa el újra az Arduino IDE-t. Vs kód most keresse meg az Arduino IDE elérési út helyesen.

### <a name="open-the-arduino-examples-folder"></a>Az Arduino Examples mappa megnyitása

Bontsa ki a bal oldali **ARDUINO EXAMPLES szakaszt,** keresse meg **az MXCHIP AZ3166 > Az AzureIoT példái című szakaszt,** és válassza a **ShakeShake**lehetőséget. Megnyílik egy új VS-kód ablak, amely a projekt mappát jeleníti meg. Ha nem látja az MXCHIP AZ3166 szakaszt, ellenőrizze, hogy az eszköz megfelelően van-e csatlakoztatva, és indítsa újra a Visual Studio-kódot.  
a ![mini-megoldás-példák](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

A mintaprojektet a parancspalettáról is megnyithatja. Kattintson a `Ctrl+Shift+P` `Cmd+Shift+P`(macOS: ) gombra a parancspaletta megnyitásához írja be az **Arduino**parancsot, majd keresse meg és válassza az **Arduino: Examples lehetőséget.**

## <a name="provision-azure-services"></a>Azure-szolgáltatások kiépítése

A megoldásablakban futtassa `Ctrl+P` a feladatot `Cmd+P`(macOS: ) a beírásával. `task cloud-provision`

A VS Code terminálon egy interaktív parancssor végigvezeti a szükséges Azure-szolgáltatások kiépítésén:

![felhő-ellátás](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Ha az oldal betöltési állapotban van, amikor megpróbál bejelentkezni az Azure-ba, olvassa el az [IoT DevKit gyakori kérdések "bejelentkezési oldal lefagy" című lépését.](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure)
 
## <a name="modify-the-hashtag"></a>A #hashtag módosítása

Nyissa `ShakeShake.ino` meg és keresse meg ezt a kódsort:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Cserélje le `iot` a kapcsos zárójelek karakterláncát a kívánt hashtagre. A DevKit később lekéri a véletlenszerű tweet, amely tartalmazza a hashtag megadott ebben a lépésben.

## <a name="deploy-azure-functions"></a>Az Azure Functions üzembe helyezése

Használja `Ctrl+P` (macOS: `Cmd+P`) `task cloud-deploy` az Azure Functions-kód üzembe helyezésének megkezdéséhez:

![felhőalapú telepítés](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Előfordulhat, hogy az Azure-függvény nem működik megfelelően. A probléma megoldásához, amikor ez bekövetkezik, tekintse meg [az IoT DevKit gyIK "fordítási hiba" című részét.](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function)

## <a name="build-and-upload-the-device-code"></a>Az eszközkód összeállítása és feltöltése

Ezután építse fel és töltse fel az eszközkódját.

### <a name="windows"></a>Windows

1. A `Ctrl+P` futtatásához `task device-upload`használható.

2. A terminál kéri, hogy lépjen be a konfigurációs módba. Ehhez tegye a következőket:

   * A gomb lenyomva tartása

   * Nyomja meg és engedje fel a reset gombot.

3. A képernyőn megjelenik a DevKit-azonosító és a "Konfiguráció".

### <a name="macos"></a>macOS

1. A DevKit konfigurációs módba való alakítása:

   Tartsa lenyomva az A gombot, majd nyomja meg és engedje fel a reset gombot. A képernyőn a "Configuration" (Konfiguráció) látható.

2. A `Cmd+P` futtatáshoz `task device-upload` állíthatja be a `task cloud-provision` lépésből beolvasott kapcsolati karakterláncot.

### <a name="verify-upload-and-run"></a>Ellenőrzés, feltöltés és futtatás

Most a kapcsolati karakterlánc be van állítva, ellenőrzi és feltölti az alkalmazást, majd futtatja. 

1. A VS Code megkezdi az Arduino vázlat ellenőrzését és feltöltését a DevKitbe:

   ![eszköz-feltöltés](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. A DevKit újraindul, és elindítja a kód futtatását.

A "Hiba: AZ3166: Ismeretlen csomag" hibaüzenet jelenhet meg. Ez a hiba akkor fordul elő, ha a táblacsomag indexe nem frissül megfelelően. A probléma megoldásához ellenőrizze az ["ismeretlen csomag" hibaüzenetet az IoT DevKit GYIK-ben.](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)

## <a name="test-the-project"></a>A projekt tesztelése

Az alkalmazás inicializálása után kattintson az A gombra, majd óvatosan rázza meg a DevKit táblát. Ez a művelet egy véletlenszerű tweetet kér le, amely a korábban megadott hashtaget tartalmazza. Néhány másodpercen belül megjelenik egy tweet a DevKit képernyőjén:

### <a name="arduino-application-initializing"></a>Arduino alkalmazás inicializálása...

![Arduino-alkalmazás-inicializálás](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Nyomja meg az A gombot a rázkódáshoz...

![Nyomja meg az A-to-shake](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Készen állok a rázásra...

![Rázkódásra kész](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Feldolgozás...

![Feldolgozás](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Nyomja meg a B olvasni ...

![Nyomja meg a-B-olvasni](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Véletlenszerű tweet megjelenítése...

![Kijelző-a-random-csipog](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Nyomja meg ismét az A gombot, majd rázza meg az új tweetet.
- Nyomja meg a B gombot, hogy végiggörgetje a tweet többi részét.

## <a name="how-it-works"></a>Működés

![diagram](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Az Arduino-vázlat egy eseményt küld az Azure IoT Hubnak. Ez az esemény elindítja az Azure Functions alkalmazást. Az Azure Functions alkalmazás tartalmazza a Twitter API-jához való csatlakozás és a tweet lekérésének logikáját. Ezután a tweet szövegét egy C2D (Felhő-eszköz) üzenetbe csomagolja, és visszaküldi az eszközre.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Nem kötelező: Használja a saját Twitter bemutatóra token

Tesztelési célokra ez a mintaprojekt egy előre konfigurált Twitter-tulajdonosi jogkivonatot használ. Azonban van egy [arány határ](https://dev.twitter.com/rest/reference/get/search/tweets) minden Twitter számla. Ha meg szeretné fontolni a saját jogkivonat használatát, kövesse az alábbi lépéseket:

1. Az új Twitter-alkalmazás regisztrálásához látogasson el a [Twitter fejlesztői portáljára.](https://dev.twitter.com/)

2. Az alkalmazás [fogyasztói kulcs- és fogyasztói titkainak beszerezése.](https://support.yapsody.com/hc/en-us/articles/360003291573-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-)

3. Használja [néhány segédprogram](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) ot, hogy létrehoz egy Twitter bemutatóra tokent ebből a két kulcsból.

4. Az [Azure Portalon](https://portal.azure.com/){:target="_blank"}, bekerüljön az **erőforráscsoportba,** és keresse meg az Azure-függvényt (Típus: App Service) a "Shake, Shake" projekthez. A név mindig tartalmazza a "shake..." Karakterlánc.

   ![azúr-függvény](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Frissítse a `run.csx` **Functions > shakeshake-cs** kódját a saját jogkivonatával:

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
   ```
  
   ![twitter-token](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Mentse a fájlt, és kattintson a **Futtatás gombra.**

## <a name="problems-and-feedback"></a>Problémák és visszajelzések

Problémák elhárítása vagy visszajelzés küldése. 

### <a name="problems"></a>Problémák

Az egyik probléma, amit lehetett látni, ha a képernyőn megjelenik a "No Tweets", miközben minden lépés sikeresen futott. Ez a feltétel általában akkor fordul elő, amikor először telepíti és futtatja a mintát, mert a függvényalkalmazás igényel bárhol néhány másodperctől akár egy perc ig hidegindítással az alkalmazás. 

Vagy a kód futtatásakor vannak olyan blips, amelyek az alkalmazás újraindítását okozzák. Ha ez a feltétel bekövetkezik, az eszközalkalmazás időtúltöltést kaphat a tweet letöltéséhez. Ebben az esetben megpróbálhatja az egyik vagy mindkét módszert a probléma megoldásához:

1. Kattintson a visszaállítás gombra a DevKit az eszköz alkalmazás ismételt futtatásához.

2. Az [Azure Portalon](https://portal.azure.com/)keresse meg a létrehozott Azure Functions alkalmazást, és indítsa újra:

   ![azure-function-restart](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>Visszajelzés

Ha egyéb problémákat tapasztal, olvassa el az [IoT DevKit GYIK-et,](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy lépjen kapcsolatba velünk a következő csatornákon:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan csatlakoztathat egy DevKit-eszközt az Azure IoT Remote Monitoring megoldásgyorsítóhoz, és hogyan kérhet le egy tweetet, az alábbi lépéseket ismertetheti:

* [Az Azure IoT remote monitoring megoldásgyorsító – áttekintés](https://docs.microsoft.com/azure/iot-suite/)