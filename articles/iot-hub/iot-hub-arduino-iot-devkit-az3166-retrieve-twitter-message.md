---
title: Az Azure Functions Twitter üzenet beolvasása |} Microsoft Docs
description: A mozgásérzékelő segítségével megrázása észleli és használja az Azure Functions egy véletlenszerű tweetet egy hashtaggel történő, melyet a megkereséséhez.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: a84393c5c53b8f8e4a8b688a462f433b2d611b0e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Rázó, egy Tweetet a Shake – letölteni egy Twitter-üzenetet az Azure Functions!

Ebben a projektben ismerje meg az Azure Functions használatával esemény elindítható a mozgásérzékelő használata. Az alkalmazás egy véletlenszerű tweetet egy #hashtag a Arduino vázlat konfigurálja a kéri le. A tweetet a DevKit képernyőn megjelenik.

## <a name="what-you-need"></a>Mi szükséges

Befejezés a [– első lépések útmutató](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) számára:

* A Wi-Fi csatlakozik DevKit rendelkezik.
* Készítse elő a fejlesztési környezetet.

Aktív Azure-előfizetés. Ha még nincs fiókja, regisztrálni keresztül ezen módszerek egyikét:

* Aktiválja a [ingyenes 30 napos próbafiókot Microsoft Azure](https://azure.microsoft.com/en-us/free/)
* Jogcím a [Azure-kreditjeinek](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) MSDN vagy a Visual Studio előfizetői esetén

## <a name="open-the-project-folder"></a>Nyissa meg a projekt mappát

### <a name="start-vs-code"></a>Indítsa el a Visual STUDIO Code

- Győződjön meg arról, hogy a DevKit **nem** csatlakoztatva a számítógéphez.
- Indítsa el a Visual STUDIO Code.
- A DevKit kapcsolódni a számítógéphez.

Visual STUDIO Code automatikusan megkeresi a DevKit és az bemutatása oldalát jeleníti meg:

![mini-solution-vscode](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_start.png)

> [!NOTE]
> Visual STUDIO Code elindításakor kaphat olyan hibaüzenetet, amely a Arduino IDE vagy a kapcsolódó tábla csomagot nem lehet található. Ha ez a hiba akkor fordul elő, zárja be a Visual STUDIO Code, és indítsa újra az Arduino IDE. VS-kódot kell most megkereséséhez Arduino IDE megfelelően.

### <a name="open-arduino-examples-folder"></a>Arduino példák mappa megnyitása

Bontsa ki a bal oldali **ARDUINO példák** szakaszban, keresse meg a **MXCHIP AZ3166 példák > AzureIoT**, és válassza ki **ShakeShake**. Ez a projekt mappát egy új Visual STUDIO Code ablak nyílik meg.

![Mini solution példák](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

Ha véletlenül bezárja a panelen, akkor nyissa meg újra. Használjon `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) nyissa meg a parancs palettát, írja be a következőt **Arduino**, keresése és válassza a **Arduino: Példák**.

## <a name="provision-azure-services"></a>Azure-szolgáltatások kiépítése

A megoldás-ablakban futtassa a feladat `Ctrl+P` (macOS: `Cmd+P`) megadásával `task cloud-provision`.

A Visual STUDIO Code terminálban egy interaktív parancssori végigvezeti Önt a szükséges Azure-szolgáltatások kiépítése:

![cloud-provision](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Ha a lap lefagy a betöltés állapota, amikor megpróbál bejelentkezni az Azure-ba, tekintse meg a [gyakran ismételt kérdések lépés] ({{"/docs/faq/#page-hangs-when-log-in-azure" |} 
 
## <a name="modify-the-hashtag"></a>A #hashtag módosítása

Nyissa meg `ShakeShake.ino` és keresse meg a kódsort:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Cserélje le a karakterlánc `iot` az előnyben részesített hashtaggel történő a kapcsos zárójelek belül. DevKit később egy véletlenszerű tweetet, amely tartalmazza az ebben a lépésben megadott hashtaggel történő kéri le.

## <a name="deploy-azure-functions"></a>Az Azure Functions üzembe helyezése

Használjon `Ctrl+P` (macOS: `Cmd+P`) futtatásához `task cloud-deploy` az Azure Functions kód telepítésének indításához:

![felhő központi telepítése](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Alkalmanként az Azure-függvény nem működnek megfelelően. Ez akkor fordul elő, ha a probléma megoldásához ellenőrizze a [gyakran ismételt kérdések lépés](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Build, és töltse fel a kód

### <a name="windows"></a>Windows

1. Használjon `Ctrl+P` futtatásához `task device-upload`.
2. A Terminálszolgáltatások konfigurációs módja megadását kéri. Ehhez tegye a következőket:

   * Tartsa lenyomva A gombot
   * Ügyfélleküldéses, és felszabadíthatja a Visszaállítás gombra.

3. A képernyőn megjelenik a DevKit azonosító és a "Konfiguráció".
4. Ez beállítja, hogy a kapcsolati karakterláncot, amely kéri le a rendszer a `task cloud-provision` lépés.
5. Visual STUDIO Code, majd elindítja a DevKit a Vázlat ellenőrzése, majd ismét feltölteni a Arduino: ![eszköz-feltöltése](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)
6. A DevKit újraindul, és elindul a kódot.

> [!NOTE]
> Előfordulhat, hogy kap egy "hiba: AZ3166: Ismeretlen csomag" hibaüzenet jelenik meg. Ez a hiba akkor fordul elő, frissítésekor a tábla csomag index nem megfelelő. A probléma megoldásához ellenőrizze a [gyakran ismételt kérdések lépés](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

### <a name="macos"></a>macOS

1. A DevKit konfigurációs üzemmódba: tartsa lenyomva a gombot A, majd leküldéses és kiadás a Visszaállítás gombra. A képernyőn megjelenik a "Konfiguráció".
2. Használjon `Cmd+P` futtatásához `task device-upload` lekért kapcsolati karakterlánc beállítása a `task cloud-provision` lépés.
3. Visual STUDIO Code, majd elindítja a DevKit a Vázlat ellenőrzése, majd ismét feltölteni a Arduino: ![eszköz-feltöltése](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)
4. A DevKit újraindul, és elindul a kódot.

> [!NOTE]
> Előfordulhat, hogy kap egy "hiba: AZ3166: Ismeretlen csomag" hibaüzenet jelenik meg. Ez a hiba akkor fordul elő, frissítésekor a tábla csomag index nem megfelelő. A probléma megoldásához ellenőrizze a [gyakran ismételt kérdések lépés](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>A projekt tesztelése

Alkalmazás inicializálása után kattintson a gombra A kiadási, majd a DevKit board óvatosan shake. Ez a művelet lekérdezi egy véletlenszerű tweetet, amely a korábban meghatározott hashtaggel történő tartalmazza. Néhány másodpercen belül egy tweetet a DevKit képernyő jelenik meg:

### <a name="arduino-application-initializing"></a>Arduino alkalmazás inicializálása...
![Arduino alkalmazás inicializálása](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Nyomja meg A a shake...
![Nyomja meg az-A-az-rázó](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Készen áll a shake...
![Készen áll-rázó](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Feldolgozás...
![Feldolgozás](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Nyomja meg a B olvasni...
![Nyomja le az B-olvasható](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Egy véletlenszerű tweetet megjelenítése...
![Display-a-random-tweet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Nyomja le az Ismét gombra, majd az új tweetet shake.
- Görgessen végig a tweetet részeinek B gomb megnyomásával.

## <a name="how-it-works"></a>Működés

![diagram](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

A Arduino vázlatot a Azure IoT hub küld egy eseményt. Ezt az eseményt akkor váltja ki az Azure Functions alkalmazás. Az Azure Functions app a Twitter tartozó API csatlakozni, és egy tweetet beolvasása a logikáját tartalmazza. Majd kezd a tweetet be egy C2D (felhő eszközre) üzenet, és küld vissza azt az eszközt.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Választható lehetőség: Saját Twitter tulajdonosi jogkivonatot használja

Tesztelési célokra, ez a minta a projekt egy előre konfigurált Twitter tulajdonosi jogkivonatot használja. Van azonban egy [sávszélesség-korlátjának](https://dev.twitter.com/rest/reference/get/search/tweets) minden Twitter-fiók. Ha azt szeretné, figyelembe kell venni a saját tokent, kövesse az alábbi lépéseket:

1. Ugrás a [Twitter fejlesztői portálján](https://dev.twitter.com/) regisztrálni egy új Twitter-alkalmazást.

2. [Felhasználói kulcs és a fogyasztói titkok](https://support.yapsody.com/hc/en-us/articles/203068116-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) az alkalmazás.

3. Használjon [néhány segédprogram](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) használatával hozzon létre egy Twitter-tulajdonosi jogkivonatot a két kulcsokat.

4. A a [Azure-portálon](https://portal.azure.com/){: target = "_blank"}, feltölti a **erőforráscsoport** keresse meg az Azure-függvény (típus: App Service) a "Shake, rázó" projekthez. A név mindig tartalmazza a "shake..." karakterláncot.
  ![azure-function](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Frissítse a kódot a `run.csx` belül **Funkciók > shakeshake-cs** saját jogkivonattal:
  ```csharp
  ...
  string authHeader = "Bearer " + "[your own token]";
  ...
  ```
  ![twitter-token](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Mentse a fájlt, és kattintson a **futtatása**.


## <a name="problems-and-feedback"></a>Problémák és visszajelzés

### <a name="the-screen-displays-no-tweets-while-every-step-has-run-successfully"></a>A képernyőn megjelenik a "No Twitter-üzeneteket", miközben minden lépés végrehajtása sikeresen befejeződött

Ez az állapot általában akkor fordul elő, először telepíteni és futtatni a mintát, mert a függvény alkalmazás szükséges, hogy bárhol egy néhány másodperc hidegindítás számára, amennyire csak egy perc alatt az alkalmazás. Vagy, ha futtatja a kódot, néhány blips, amelyek a az alkalmazás újraindítása. Ez az állapot akkor fordul elő, amikor az eszköz alkalmazás való beolvasásához használt a tweetet időtúllépés kérheti le. Ebben az esetben megpróbálhatja a probléma megoldásához a módszerek közül:

1. A Visszaállítás gombra az eszköz alkalmazás újra futtatni a DevKit.

2. Az a [Azure-portálon](https://portal.azure.com/), keresse meg az Azure Functions létrehozott alkalmazást, és indítsa újra a: ![azure-függvény-újraindítás](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>Visszajelzés

Ha más problémákat tapasztal, tekintse meg a [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy kapcsolatba velünk a következő csatornák:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik megismerte a DevKit eszköz csatlakoztatása az Azure IoT Suite és lekérni egy tweetet, az alábbiakban a javasolt lépéseket:

* [Az Azure IoT Suite – áttekintés](https://docs.microsoft.com/azure/iot-suite/)