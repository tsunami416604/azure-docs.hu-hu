---
title: Hozzon létre egy IoT DevKit translator, az Azure Functions és a Cognitive Services |} A Microsoft Docs
description: A mikrofon használatára egy IoT DevKit a hangalapú üzenetet kap, majd az Azure Cognitive Services a lefordított szöveg angol nyelven történő feldolgozás
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.openlocfilehash: 394cb2d77d77772bd7ead82f0937a186472f5229
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533374"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Az Azure Functions és a Cognitive Services IoT DevKit AZ3166 használata nyelvű fordító

Ebből a cikkből elsajátíthatja IoT DevKit, egy nyelv translator használatával győződjön meg arról, hogyan [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/). Azt rögzíti a hangját felhőplatformot azt, és a fejlesztői készlet képernyőn megjelenő angol nyelvű szöveget.

A [MXChip IoT DevKit](https://aka.ms/iot-devkit) egy teljes körű Arduino kompatibilis tábla a gazdag perifériák és érzékelők van. A használatával is fejleszthet [Azure IoT-eszköz Workbench](https://aka.ms/iot-workbench) vagy [Azure IoT-eszközök](https://aka.ms/azure-iot-tools) bővítőcsomagjának Visual Studio Code-ban. A [projektek katalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) mintaalkalmazások prototípus IoT-megoldások segítségével tartalmazza.

## <a name="before-you-begin"></a>Előkészületek

Ez az oktatóanyag lépéseinek végrehajtásához először a következő feladatokat végezheti el:

* Készítse elő a DevKit a lépéseket követve [IoT DevKit AZ3166 csatlakoztatása az Azure IoT hubba a felhőben](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="create-azure-cognitive-service"></a>Az Azure Cognitive Services-szolgáltatás létrehozása

1. Az Azure Portalon kattintson a **erőforrás létrehozása** és keressen rá a **Speech**. A beszédfelismerési szolgáltatás létrehozása az űrlap kitöltésekor.
  ![Beszédszolgáltatás](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. Nyissa meg a Speech service, imént létrehozott, kattintson **kulcsok** szakaszt másolja ki és jegyezze fel a **Key1** DevKit eléréséhez szükséges hozzá.
  ![Kulcsok másolása](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Nyissa meg a mintaprojektet

1. Győződjön meg arról, hogy az IoT DevKit **nincs csatlakoztatva** a számítógépre. Először indítsa el a VS Code, és a fejlesztői készlet csatlakoztatása a számítógéphez.

1. Kattintson a `F1` a parancskatalógus megnyitásához, írja be, és válassza ki **Azure IoT-eszköz Workbench: Példák megnyitása...** . Válassza ki **IoT DevKit** , tábla.

1. Az IoT Workbench példák oldalán található **DevKit Translator** kattintson **nyílt minta**. Ezután kiválasztja a mintakód letöltése az alapértelmezett elérési utat.
  ![Nyissa meg a minta](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Beszédszolgáltatás használja az Azure Functions használatával

1. A VS Code-ban kattintson `F1`, írja be, és válassza ki **Azure IoT-eszköz Workbench: Azure-szolgáltatások üzembe helyezése...** . ![Azure-szolgáltatások üzembe helyezése](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Kövesse a lépéseket az Azure IoT Hub és az Azure Functions üzembe helyezése.
  ![Kiépítés lépések](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

  Jegyezze fel az Azure IoT Hub-eszköznév hozott létre.

1. Nyissa meg `Functions\DevKitTranslatorFunction.cs` , és frissítse az alábbi kódsorokkal az eszköz nevét és útmutatóban lejegyzett beszédszolgáltatás kulcs.
  ```csharp
  // Subscription Key of Speech Service
  const string speechSubscriptionKey = "";

  // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
  const string speechServiceRegion = "";

  // Device ID
  const string deviceName = "";
  ```

1. Kattintson a `F1`, írja be, és válassza ki **Azure IoT-eszköz Workbench: Helyezze üzembe az Azure...** . Ha a VS Code újbóli üzembe helyezés megerősítést kér, kattintson a **Igen**.
  ![Figyelmeztetés üzembe helyezése](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. Győződjön meg arról, hogy az üzembe helyezés sikeres.
  ![Sikeres üzembe helyezése](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. Az Azure Portalon lépjen a **Function Apps** területén keresse meg az Azure-függvényalkalmazást az imént létrehozott. Kattintson a `devkit_translator`, majd kattintson a **<> / függvény URL-Címének lekérése** az URL vágólapra másolásához.
  ![Függvény URL-Címének másolása](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. Illessze be az URL-CÍMÉT az `azure_config.h` fájlt.
  ![Az Azure-config](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

  > [!NOTE]
  > A függvényalkalmazás nem működik megfelelően, ha bejelöli ezt [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) szakasz feloldásához.

## <a name="build-and-upload-device-code"></a>Hozhat létre és töltse fel az eszköz kód

1. A fejlesztői készlet való váltás **konfigurációs mód** szerint:
  * Tartsa lenyomva a gomb **A**.
  * Nyomja le az és kiadási **alaphelyzetbe** gombra.

  Megjelenik a képernyőn megjelenik a fejlesztői készlet azonosítója és **konfigurációs**.

  ![Fejlesztői készlet konfigurációs mód](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Kattintson a `F1`, írja be, és válassza ki **Azure IoT-eszköz Workbench: -Beállítások konfigurálása... > konfigurációs eszköz kapcsolati karakterláncának**. Válassza ki **IoT Hub eszköz kapcsolati karakterláncának kiválasztása** a DevKit való konfigurálásához.
  ![Kapcsolati karakterlánc konfigurálása](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. Miután sikeresen befejeződött az értesítés jelenik meg.
  ![Kapcsolati karakterlánc sikeres konfigurálása](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. Kattintson a `F1` ismét gépelje be, és válassza ki **Azure IoT-eszköz Workbench: Töltse fel az eszköz kód**. Azt a fordítási elindul, és töltse fel a kód DevKit.
  ![Eszköz feltöltése](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

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

![mini-solution-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

Az IoT DevKit rögzíti a hangját, majd közzéteszi a HTTP-kérést az Azure Functions eseményindítóra. Az Azure Functions meghívja a cognitive Services-szolgáltatás a beszédfordító API-t ehhez a fordítás. Miután az Azure Functions lekérdezi a fordítandó szöveg, C2D üzenetet küld az eszközre. Majd a fordítást a képernyő jelenik meg.

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákat tapasztal, tekintse meg a [IoT DevKit – gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy fel velünk a kapcsolatot a következő csatornák használatával:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
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
