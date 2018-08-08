---
title: Az Azure Functions Twitter-üzenet lekérdezése |} A Microsoft Docs
description: Mozgásérzékelő segítségével észlelheti, miközben és a egy Ön által megadott hashtaggel véletlenszerű tweet keresése az Azure Functions használatával
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: 33d7c7f7f2e127647b43a62541fbc29c8417743c
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39595994"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Rázó, egy tweetet rázza meg – az Azure Functions Twitter-üzenet lekérdezése

Ebben a projektben elsajátíthatja az Azure Functions szolgáltatással esemény aktiválása a mozgásérzékelő használatával. Az alkalmazás lekéri az Arduino rajz konfigurál egy #hashtag véletlenszerű tweet közzététele. A tweet DevKit képernyőjén jeleníti meg.

## <a name="what-you-need"></a>Mi szükséges

Fejezze be a [– első lépések útmutató](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) való:

* A fejlesztői készlet Wi-Fi csatlakozik.
* A fejlesztési környezet előkészítését.

Aktív Azure-előfizetés. Ha még nincs fiókja, ezek a módszerek egyike segítségével regisztrálhatja:

* Aktiválja a [ingyenes 30 napos próbafiókra Microsoft Azure](https://azure.microsoft.com/free/)
* Jogcím a [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) MSDN vagy a Visual Studio-előfizetők

## <a name="open-the-project-folder"></a>A projektmappa fájllistájának megnyitásához.

Először nyissa meg a projektmappában. 

### <a name="start-vs-code"></a>Indítsa el a VS Code

* Ellenőrizze, hogy a számítógép csatlakozik a fejlesztői készlet.

* Indítsa el a VS Code.

* A fejlesztői készlet csatlakoztatása a számítógéphez.

   > [!NOTE]
   > A VS Code elindításához, is megjelenhet egy hibaüzenet, amely nem lehet az Arduino IDE vagy kapcsolódó tábla csomag található. Ha ez a hiba akkor fordul elő, zárja be a VS Code, és indítsa újra az Arduino IDE. Megfelelően a VS Code most kell megkeresheti az Arduino IDE elérési utat.

### <a name="open-the-arduino-examples-folder"></a>Az Arduino példák mappa megnyitása

Bontsa ki a bal oldali **ARDUINO példák** területén keresse meg a **példák az MXCHIP AZ3166 > AzureIoT**, és válassza ki **ShakeShake**. Megnyílik egy új VS Code-ablak, a projektmappa fájllistájának megjelenítése. Ha nem látja az MXCHIP AZ3166 szakaszban, győződjön meg arról, hogy az eszköz megfelelően csatlakoztatva van, és indítsa újra a Visual Studio Code.  
a ![mini-solution-példák](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

A mintaprojekt a parancskatalógus is megnyithatja. Kattintson a `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) a parancskatalógus megnyitásához, írja be a **Arduino**, keresse meg és válassza a **Arduino: Példák**.

## <a name="provision-azure-services"></a>Azure-szolgáltatások üzembe helyezése

A megoldás ablakban futtassa a feladat `Ctrl+P` (macOS: `Cmd+P`) megadásával `task cloud-provision`.

A VS Code terminálban egy interaktív parancssori végigvezeti Önt a szükséges Azure-szolgáltatások kiépítése:

![cloud-provision](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Ha az oldal lefagy a betöltés állapota, amikor megpróbál bejelentkezni Azure-ba, olvassa el a ["bejelentkezési oldal lefagy" lépés a IoT DevKit – gyakori kérdések a](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure).
 
## <a name="modify-the-hashtag"></a>A #hashtag módosítása

Nyissa meg `ShakeShake.ino` , és keresse a sor kódot:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Cserélje le a karakterláncot `iot` belül az előnyben részesített hashtaggel kapcsos zárójeleket kell használni. A fejlesztői készlet később egy véletlenszerű tweet, amely tartalmazza az ebben a lépésben megadott hashtaggel kérdezi le.

## <a name="deploy-azure-functions"></a>Az Azure Functions üzembe helyezése

Használat `Ctrl+P` (macOS: `Cmd+P`) futtatásához `task cloud-deploy` üzembe helyezése az Azure Functions kódjaiba elindításához:

![felhő üzembe helyezése](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Néha előfordul az Azure-függvény nem működik megfelelően. Ez akkor fordul elő, ha a probléma megoldásához ellenőrizze a ["lekérdezésfordítási hiba" szakaszában az IoT DevKit – gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Hozhat létre és töltse fel az eszköz kódot

Ezután hozhat létre, és töltse fel az eszköz kódot.

### <a name="windows"></a>Windows

1. Használat `Ctrl+P` futtatásához `task device-upload`.

2. A terminálban kéri, hogy adja meg a konfigurációs mód. Ehhez tegye a következőket:

   * A gombot lenyomva

   * Leküldéses és engedje el a Visszaállítás gombra.

3. A képernyőn megjelenik a fejlesztői készlet azonosítója és a "Konfiguráció".

### <a name="macos"></a>macOS

1. A fejlesztői készlet elhelyezi a konfigurációs mód:

   A gombot lenyomva, majd küldje le, és engedje a Visszaállítás gombra. A képernyőn megjelenik a "Konfiguráció".

2. Használat `Cmd+P` futtatásához `task device-upload` , beállítjuk a kapcsolati karakterláncot, amely a rendszer beolvassa a `task cloud-provision` . lépés.

### <a name="verify-upload-and-run"></a>Győződjön meg arról, töltse fel és futtatása

Most a kapcsolati karakterlánc beállítása, azt ellenőrzi, és feltölti az alkalmazást, majd futtatja. 

1. A VS Code ellenőrzése és az Arduino rajz tölt fel a DevKit kezdődik:

   ![eszköz-feltöltést](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. A fejlesztői készlet újraindul, és elindítja a kód futtatása.

Előfordulhat, hogy kap egy "hiba: AZ3166: Ismeretlen csomag" hibaüzenet jelenik meg. Ez a hiba akkor fordul elő, ha a tábla csomagindexet nem frissül megfelelően. A probléma elhárítása érdekében ellenőrizze a ["Ismeretlen csomag" hiba a IoT DevKit – gyakori kérdések a](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>A projekt tesztelése

Alkalmazás inicializálása, után kattintson a gombra A kiadási, majd óvatosan rázza meg az DevKit üzenőfal. Ez a művelet lekérdezi egy véletlenszerű tweet, amely tartalmazza a korábban megadott hashtaggel. Néhány másodpercen belül egy tweetet a DevKit képernyőn jelenik meg:

### <a name="arduino-application-initializing"></a>Arduino alkalmazás inicializálása...

![Arduino-alkalmazás inicializálása](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Nyomja le a rázza meg A...

![Nyomja le – egy-az-rázó](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Készen áll a rázza meg...

![Kész – rázó](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Feldolgozás...

![Feldolgozás](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Nyomja meg a B olvasható...

![Nyomja meg B-olvasható](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Egy véletlenszerű tweet megjelenítése...

![Display-a-random-tweet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Nyomja meg ismét gombra, majd egy új tweetet rázza meg.
- Görgessen végig a Twitter-üzenetet a többi B gomb megnyomásával.

## <a name="how-it-works"></a>Működés

![Diagram](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Az Arduino rajz eseményt küld az Azure IoT hubra. Ezt az eseményt akkor indítja az Azure Functions-alkalmazás. Az Azure Functions-alkalmazás a Twitter API-hoz csatlakozhat, és beolvasni egy tweetet logikát tartalmaz. Azt a C2D, majd burkolja a tweet szövege (felhőből az eszközre irányuló) üzenet, és küld vissza az eszközön.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Választható lehetőség: A saját Twitter-tulajdonosi jogkivonat használata

A mintaprojekt tesztelési célokra használja az előre konfigurált Twitter tulajdonosi jogkivonattal. Van azonban egy [sávszélesség-korlátjának](https://dev.twitter.com/rest/reference/get/search/tweets) minden Twitter-fiók. Ha azt szeretné, érdemes megfontolnia a saját token használatát, kövesse az alábbi lépéseket:

1. Lépjen a [Twitter-fejlesztői portál](https://dev.twitter.com/) Twitteren új alkalmazás regisztrálásához.

2. [Fogyasztói kulcs és a fogyasztói titkos kódok lekérése](https://support.yapsody.com/hc/en-us/articles/203068116-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) az alkalmazás.

3. Használat [néhány segédprogram](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) használatával hozzon létre egy Twitter-tulajdonosi jogkivonat ezen két kulcsot.

4. Az a [az Azure portal](https://portal.azure.com/){: target = "_blank"}, kaphat a **erőforráscsoport** , és keresse meg az Azure-függvény (típusa: App Service-ben) a "Rázó, rázó" projekt. A név mindig tartalmazza a "rázó..." karakterlánc.

   ![az Azure-függvény](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. A kód frissítése `run.csx` belül **Funkciók > shakeshake-cs** saját jogkivonattal:

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
  ```
  
  ![Twitter-token](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Mentse a fájlt, majd kattintson a **futtatása**.

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Hogyan kapcsolatos hibák elhárítása, vagy visszajelzést. 

### <a name="problems"></a>Problémák

Az egyik probléma láthatóvá teheti, ha a képernyő "No Tweetek" jelenít meg, amíg minden lépés végrehajtása sikeresen befejeződött. Ez az állapot általában akkor fordul elő, üzembe helyezése, és futtassa a mintát, mert a függvényalkalmazáshoz szükséges bárhol a néhány másodperc a hidegindítás kevesebb mint egy perc alatt az alkalmazás első alkalommal. 

Vagy a kód futtatásakor vannak bizonyos jelekből, amely egy, az alkalmazás újraindítása miatt. Ez az állapot akkor fordul elő, amikor az eszköz alkalmazás kérheti le a tweet beolvasása közben időtúllépés. Ebben az esetben megpróbálhatja újból az egyik vagy mindkét módszer a probléma megoldásához:

1. A Visszaállítás gombra a DevKit újra futtatni az eszközalkalmazáshoz.

2. Az a [az Azure portal](https://portal.azure.com/), az Azure Functions-alkalmazás hozott létre, és indítsa újra:

   ![az Azure-függvény-újraindítás](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>Visszajelzés

Ha más problémákat tapasztal, tekintse meg a [IoT DevKit – gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) , vagy lépjen kapcsolatba velünk a kapcsolatot a következő csatornákon:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik egy DevKit eszköz csatlakoztatása az Azure IoT távoli figyelési megoldásgyorsító és beolvasni egy tweetet, Íme a javasolt következő lépések:

* [Az Azure IoT távoli figyelési megoldásgyorsító áttekintése](https://docs.microsoft.com/azure/iot-suite/)