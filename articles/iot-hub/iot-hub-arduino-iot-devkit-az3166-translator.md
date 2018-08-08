---
title: Hozzon létre egy IoT DevKit translator, az Azure Functions és a Cognitive Services |} A Microsoft Docs
description: A mikrofon használatára egy IoT DevKit a hangalapú üzenetet kap, majd az Azure Cognitive Services a lefordított szöveg angol nyelven történő feldolgozás
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/28/2018
ms.author: liydu
ms.openlocfilehash: acfff95afacfa1e5c75a799ba84d64cfa0579f66
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592091"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Az Azure Functions és a Cognitive Services IoT DevKit AZ3166 használata nyelvű fordító

Ebből a cikkből elsajátíthatja IoT DevKit, egy nyelv translator használatával győződjön meg arról, hogyan [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/). Azt rögzíti a hangját felhőplatformot azt, és a fejlesztői készlet képernyőn megjelenő angol nyelvű szöveget.

A [MXChip IoT DevKit](https://aka.ms/iot-devkit) egy teljes körű Arduino kompatibilis tábla a gazdag perifériák és érzékelők van. A használatával is fejleszthet [Arduino a Visual Studio Code-bővítmény](https://aka.ms/arduino). És az egyre növekvő együtt származik [projektek katalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) útmutatásként prototípus Internet of Things (IoT) megoldás, amely a Microsoft Azure-szolgáltatások előnyeit.

## <a name="what-you-need"></a>Mi szükséges

Fejezze be a [– első lépések útmutató](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) való:

* A fejlesztői készlet Wi-Fi csatlakozik
* A fejlesztési környezet előkészítése

Aktív Azure-előfizetés. Ha nem rendelkezik egy, a két módszer egyikével keresztül regisztrálhatja:

* Aktiválja a [ingyenes 30 napos próbafiókra Microsoft Azure](https://azure.microsoft.com/free/)
* Jogcím a [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) MSDN vagy a Visual Studio-előfizetők

## <a name="open-the-project-folder"></a>A projektmappa fájllistájának megnyitásához.

Első lépésként nyissa meg a projektmappában. 

### <a name="start-vs-code"></a>Indítsa el a VS Code

- Ellenőrizze, hogy a számítógép csatlakozik a fejlesztői készlet.

- Indítsa el a VS Code.

- A fejlesztői készlet csatlakoztatása a számítógéphez.

### <a name="open-the-arduino-examples-folder"></a>Az Arduino példák mappa megnyitása

Bontsa ki a bal oldali **ARDUINO példák > Példák az MXCHIP AZ3166 > AzureIoT**, és válassza ki **DevKitTranslator**. Megnyílik egy új VS Code-ablak, a projektmappa fájllistájának megjelenítése. Ha nem látja az MXCHIP AZ3166 szakasz példákat, győződjön meg arról, hogy az eszköz megfelelően csatlakoztatva van, és indítsa újra a VS Code.  

![IoT DevKit minták](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_examples.png)

A példában a parancskatalógus is megnyithatja. Használat `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) a parancskatalógus megnyitásához, írja be a **Arduino**, keresse meg és válassza a **Arduino: Példák**.

## <a name="provision-azure-services"></a>Azure-szolgáltatások üzembe helyezése

A megoldás ablakban írja be a `Ctrl+P` (macOS: `Cmd+P`), és adja meg `task cloud-provision`.

A Terminálszolgáltatások VS Code-ban egy interaktív parancssori végigvezeti Önt a szükséges Azure-szolgáltatások üzembe helyezése:

![Felhő üzembe helyezése feladat](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-provision.png)

## <a name="deploy-the-azure-function"></a>Az Azure-függvény üzembe helyezése

Használat `Ctrl+P` (macOS: `Cmd+P`) futtatásához `task cloud-deploy` üzembe helyezéséhez az Azure Functions-kódhoz. Ez a folyamat általában 2 – 5 percet vesz igénybe.

![Felhőalapú feladat üzembe helyezése](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-deploy.png)

Miután az Azure-függvény sikeresen üzembe helyezte, adja meg a függvényalkalmazás neve azure_config.h fájlt. Navigálhat [az Azure portal](https://portal.azure.com/) megjelenítéséhez:

![Keresse meg az Azure-Függvényalkalmazás neve](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-function.png)

> [!NOTE]
> Ha az Azure-függvény nem működik megfelelően, ellenőrizze a [az IoT DevKit – gyakori kérdések lapján "complication hiba az Azure-függvényt"](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) feloldásához.

## <a name="build-and-upload-the-device-code"></a>Hozhat létre és töltse fel az eszköz kódot

1. Használat `Ctrl+P` (macOS: `Cmd+P`) futtatásához `task config-device-connection`.

2. A terminál ekkor megkérdezi, hogy a kapcsolati karakterláncot, amely lekéri a használni kívánt `task cloud-provision` lépést. Azt is megadhatja a saját eszköz kapcsolati karakterláncot, kiválasztásával **"Új létrehozása..."**

3. A terminálban kéri, hogy adja meg a konfigurációs mód. Ehhez A gombot lenyomva, majd küldje le, és engedje a Visszaállítás gombra. A képernyőn megjelenik a fejlesztői készlet azonosítója és a "Konfiguráció".

   ![Ellenőrzés és feltöltése az Arduino áttekintése](media/iot-hub-arduino-iot-devkit-az3166-translator/config-device-connection.png)

4. Miután `task config-device-connection` befejeződött, kattintson `F1` betölteni a VS Code parancsokat, és válassza ki `Arduino: Upload`, majd elindítja a VS Code ellenőrzése és feltöltése az Arduino Vázlat.

   ![Ellenőrzés és feltöltése az Arduino áttekintése](media/iot-hub-arduino-iot-devkit-az3166-translator/arduino-upload.png)

A fejlesztői készlet újraindul, és elindítja a kód futtatása.

## <a name="test-the-project"></a>A projekt tesztelése

Alkalmazás inicializálása, után kövesse az utasításokat a DevKit képernyőn. Az alapértelmezett adatforrás nyelv az kínai.

Egy másik nyelv fordítási célú kiválasztása:

1. Nyomja le a gomb A telepítési módját adja meg.

2. Görgessen a forrás összes támogatott nyelvet B gomb megnyomásával.

3. Nyomja le a Forrásnyelv erősítse meg A gombot.

4. Nyomja le az közben tartsa lenyomva a B gomb, majd kiadási kezdeményezni a fordítás B gomb.

5. A lefordított szöveg angol nyelvű jeleníti meg a képernyőn.

![Görgessen a nyelv kiválasztása](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Fordítási eredménye](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

A fordítási eredmény képernyőn a következő műveletek végezhetők el:

- Nyomja le a gombok A és B görgessen és válassza ki a forrás.

- A B gombra való kommunikációhoz. A szóbeli küldésére, és a fordítás szöveget, engedje el a B gombot.

## <a name="how-it-works"></a>Működés

![Mini-Solution-Voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

Az Arduino rajz rekordok a hangját, majd közzéteszi egy HTTP-trigger egy Azure-függvény kérelmet. Az Azure-függvény meghívja a cognitive Services-szolgáltatás a beszédfordító API-t ehhez a fordítás. Miután az Azure-függvény beolvassa a fordítandó szöveg, C2D (felhőből az eszközre irányuló) üzenetet küld az eszköz. Majd a fordítást a képernyő jelenik meg.

## <a name="change-device-id"></a>Változás-Eszközazonosító

Az alapértelmezett eszköz azonosítója az Azure IoT Hub regisztrálva van **AZ3166**. Az eszköz azonosítója módosítása esetén lásd: hogyan [az IoT-eszköz azonosítója testre a fejlesztői készlet](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákat tapasztal, tekintse meg a [IoT DevKit – gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy fel velünk a kapcsolatot a következő csatornák használatával:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Hogy megismerte a fordító a IoT DevKit használata az Azure Functions és a Cognitive Services használatával. Ebben az útmutatóban megtanulta, hogyan lehet:

> [!div class="checklist"]
> * A Visual Studio Code feladat segítségével automatizálhatja a felhőbeli rendelkezések
> * Azure IoT-eszköz kapcsolati karakterlánc konfigurálása
> * Az Azure-függvény üzembe helyezése
> * A szóbeli üzenet fordítás tesztelése

Folytassa a további kapcsolatos további információt:

> [!div class="nextstepaction"]
> [IoT DevKit AZ3166 csatlakozhat az Azure IoT távoli figyelési megoldásgyorsító](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)
