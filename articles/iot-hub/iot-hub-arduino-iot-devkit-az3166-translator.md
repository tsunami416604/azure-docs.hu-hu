---
title: Fordító idonylá, Kognitív szolgáltatások, IoT DevKit
description: Az IoT DevKit mikrofonjával hangüzenetet kaphat, majd az Azure Cognitive Services segítségével feldolgozhatja azt lefordított szöveggé angol nyelven
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.openlocfilehash: 8e840a1ae7161ea3e7b370889a1f0fb648ca120e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953358"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Az IoT DevKit AZ3166 használata az Azure Functions és a Cognitive Services szolgáltatással nyelvfordítóvé

Ebben a cikkben megtudhatja, hogyan teheti az IoT DevKit-et nyelvi fordítóként az [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)használatával. Rögzíti a hangját, és lefordítja a DevKit képernyőn megjelenő angol szövegre.

Az [MXChip IoT DevKit](https://aka.ms/iot-devkit) egy all-in-one Arduino kompatibilis alaplap gazdag perifériákkal és érzékelőkkel. Az [Azure IoT Device Workbench](https://aka.ms/iot-workbench) vagy [az Azure IoT Tools](https://aka.ms/azure-iot-tools) bővítménycsomag gal fejleszthet a Visual Studio-kódban. A [projektkatalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) mintaalkalmazásokat tartalmaz az IoT-megoldások prototípusának prototípusához.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag lépéseinek végrehajtásához először hajtsa végre a következő feladatokat:

* Készítse elő a DevKit-et az [IoT DevKit AZ3166 csatlakoztatása az Azure IoT Hubhoz a felhőben](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)című lépések végrehajtásával.

## <a name="create-azure-cognitive-service"></a>Azure Cognitive Service létrehozása

1. Az Azure Portalon kattintson az **Erőforrás létrehozása** elemre, és keressen **beszédfelismerést.** Töltse ki az űrlapot a beszédszolgáltatás létrehozásához.
  ![Speech szolgáltatás](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. Nyissa meg az imént létrehozott beszédfelismerési szolgáltatást, kattintson a **Kulcsok** szakaszra a DevKit hozzáféréséhez tartozó **Key1** billentyűlemásolásához.
  ![Billentyűk másolása](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Mintaprojekt megnyitása

1. Győződjön meg arról, hogy az IoT DevKit **nincs csatlakoztatva** a számítógéphez. Először indítsa el a VS-kódot, majd csatlakoztassa a DevKit-et a számítógéphez.

1. Kattintson ide `F1` a parancspaletta megnyitásához, írja be és válassza az **Azure IoT Device Workbench: Open Examples...** lehetőséget. Ezután válassza **az IoT DevKit** fórumon.

1. Az IoT Workbench-példák lapon keresse meg a **DevKit-fordítót,** és kattintson a **Minta megnyitása gombra.** Ezután kiválasztja a mintakód letöltéséhez vezető alapértelmezett elérési utat.
  ![Nyitott minta](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Beszédfelismerési szolgáltatás használata az Azure-függvényekkel

1. A VS-kód `F1`ban kattintson a gombra, és válassza az **Azure IoT-eszköz munkapadját: Azure-szolgáltatások kiépítése...**. ![Azure-szolgáltatások kiépítése](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Kövesse az Azure IoT Hub és az Azure Functions kiépítésének befejezéséhez szükséges lépéseket.
   ![A rendelkezésre bocsátás lépései](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   Jegyezze fel az Azure IoT Hub-eszköz név létrehozott.

1. Nyissa `Functions\DevKitTranslatorFunction.cs` meg és frissítse a következő kódsorokat a feljegyzett eszköznévvel és beszédszolgáltatás-kulccsal.
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. Kattintson a gombra, `F1`írja be, és válassza az Azure **IoT-eszköz munkapadját: Üzembe helyezés az Azure-ban...**. Ha a VS Code megerősítést kér az átcsoportosításról, kattintson az **Igen**gombra.
   ![Figyelmeztetés telepítése](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. Győződjön meg arról, hogy a központi telepítés sikeres.
   ![Sikeres üzembe helyezés](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. Az Azure Portalon nyissa meg a **Functions Apps szakaszt,** keresse meg az éppen létrehozott Azure Function alkalmazást. Kattintson `devkit_translator`a gombra, majd az URL másolásához kattintson **</> Függvény URL-címének bemásolása.**
   ![Függvény URL-címének másolása](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. Illessze be `azure_config.h` az URL-címet a fájlba.
   ![Azure-konfiguráció](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

   > [!NOTE]
   > Ha a Függvény alkalmazás nem működik megfelelően, ellenőrizze ezt [a gyIK](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) szakaszt a probléma megoldásához.

## <a name="build-and-upload-device-code"></a>Eszközkód összeállítása és feltöltése

1. A DevKit **konfigurációs üzemmódba** kapcsolása:
   * Tartsa lenyomva az **A**gombot.
   * Nyomja meg és engedje el **a Reset** gombot.

   Látni fogja, hogy a képernyőn megjelenik a DevKit-azonosító és **a Konfiguráció**.

   ![DevKit konfigurációs mód](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Kattintson a gombra, `F1`és válassza az Azure **IoT-eszköz munkapadját: Eszközbeállítások konfigurálása... > a konfigurációs eszköz kapcsolati karakterláncát.** Válassza **az IoT hub eszköz kapcsolati karakterláncának kiválasztása** a DevKit konfigurálásához.
   ![Kapcsolati karakterlánc konfigurálása](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. Az értesítés a sikeres sikeres elvégzését követően jelenik meg.
   ![A kapcsolati karakterlánc sikeressének konfigurálása](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. Kattintson ismét, írja be, és válassza **az Azure IoT-eszköz munkapad: Eszközkód feltöltése**. `F1` Elindítja a fordítást, és feltölti a kódot a DevKit-be.
   ![Eszköz feltöltése](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>A projekt tesztelése

Az alkalmazás inicializálása után kövesse a DevKit képernyőn megjelenő utasításokat. Az alapértelmezett forrásnyelv a kínai.

Másik fordítási nyelv kiválasztása:

1. A beállítási módba való belépéshez nyomja meg az A gombot.

2. Az Összes támogatott forrásnyelv görgetéséhez nyomja meg a B gombot.

3. Nyomja meg az A gombot a választott forrásnyelv megerősítéséhez.

4. Beszéd közben nyomja meg és tartsa lenyomva a B gombot, majd engedje el a B gombot a fordítás elindításához.

5. A lefordított szöveg angolul jelenik meg a képernyőn.

![Görgetés a nyelv kiválasztásához](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Fordítás eredménye](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

A fordítás eredményképernyőjén a következőket teheti:

- Az A és a B gombnyomással görgetheti és kiválaszthatja a forrásnyelvet.

- A b gombot megnyomva beszél. A hang elküldéséhez és a fordítás szövegének beküldéséhez engedje fel a B gombot.

## <a name="how-it-works"></a>Működés

![mini-megoldás-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

Az IoT DevKit rögzíti a hangját, majd egy HTTP-kérést tesz közzé az Azure Functions aktiválásához. Az Azure Functions meghívja a kognitív szolgáltatás beszédfordító API-t a fordításhoz. Miután az Azure Functions leeredményes a fordítási szöveget, c2D-üzenetet küld az eszköznek. Ezután a fordítás megjelenik a képernyőn.

## <a name="problems-and-feedback"></a>Problémák és visszajelzések

Ha problémákat tapasztal, olvassa el az [IoT DevKit GYIK-et,](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy vegye fel velünk a felvelünk a következő csatornákat:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Megtanulta, hogyan használhatja az IoT DevKit-et fordítóként az Azure Functions és a Cognitive Services használatával. Ebben a how-to, megtanulta, hogyan kell:

> [!div class="checklist"]
> * Felhőalapú rendelkezések automatizálása a Visual Studio Code feladattal
> * Az Azure IoT-eszköz kapcsolati karakterláncának konfigurálása
> * Az Azure-függvény üzembe helyezése
> * A hangüzenet fordításának tesztelése

Előre a többi oktató, hogy megtanulják:

> [!div class="nextstepaction"]
> [Csatlakoztassa az IoT DevKit AZ3166-ot az Azure IoT remote monitoring megoldásgyorsítóhoz](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)
