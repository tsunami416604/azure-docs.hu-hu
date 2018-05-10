---
title: Az Azure-függvény és kognitív szolgáltatások használatával IoT DevKit fordító |} Microsoft Docs
description: Az IoT-DevKit hangüzenet és Azure kognitív szolgáltatásainak lefordított szöveg angol nyelven történő feldolgozás mikrofon használatát.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hube
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2018
ms.author: liydu
ms.openlocfilehash: 20e5a5f4fb381dedc42d698464819a6098c3579b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="use-iot-devkit-az3166-with-azure-function-and-cognitive-services-to-make-a-language-translator"></a>Az Azure-függvény és kognitív szolgáltatásokkal IoT DevKit AZ3166 használata nyelvi a fordító

Ebből a cikkből megtanulhatja, nyelvi a fordító IoT DevKit végezzen [Azure kognitív szolgáltatások](https://azure.microsoft.com/services/cognitive-services/). Rögzíti a hang és a következőkből fordítja le a DevKit képernyőn megjelenő angol szöveg.

A [MXChip IoT DevKit](https://aka.ms/iot-devkit) egy mindent egy Arduino kompatibilis board gazdag perifériák és érzékelők van. A használatával fejleszthet [Arduino Visual Studio Code kiterjesztése](https://aka.ms/arduino). Az érkezett egy növekvő [projektek katalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) hogy végigvezesse prototípus az eszközök internetes hálózatát (IoT) megoldások, amelyek a Microsoft Azure-szolgáltatások előnyeit.

## <a name="what-you-need"></a>Mi szükséges

Befejezés a [– első lépések útmutató](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) számára:

* A Wi-Fi csatlakozik DevKit rendelkezik
* A fejlesztési környezet előkészítése

Aktív Azure-előfizetés. Ha még nem rendelkezik ilyennel, ezek két módszer használatával regisztrálhatja:

* Aktiválja a [ingyenes 30 napos próbafiókot Microsoft Azure](https://azure.microsoft.com/free/)
* Jogcím a [Azure-kreditjeinek](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) MSDN vagy a Visual Studio előfizetői esetén

## <a name="step-1-open-the-project-folder"></a>1. lépés Nyissa meg a projekt mappát

### <a name="a-start-vs-code"></a>A. Indítsa el a Visual STUDIO Code

- Győződjön meg arról, hogy a DevKit nincs csatlakoztatva a Számítógéphez.
- Indítsa el a Visual STUDIO Code
- A DevKit kapcsolódni a számítógéphez.

### <a name="b-open-the-arduino-examples-folder"></a>B. Nyissa meg a Arduino példák

Bontsa ki a bal oldali **ARDUINO példák > MXCHIP AZ3166 példák > AzureIoT**, és válassza ki **DevKitTranslator**. Egy új Visual STUDIO Code ablak nyílik meg benne DEVKITTRANSLATOR projektmappában.

![Az IoT-DevKit minták](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_examples.png)

> [!NOTE]
> Példa parancs palettáról is megnyithatja. Használjon `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) nyissa meg a parancs palettát, írja be a következőt **Arduino**, keresése és válassza a **Arduino: Példák**.

## <a name="step-2-provision-azure-services"></a>2. lépés Azure-szolgáltatások kiépítése

A megoldás ablakban írja be a `Ctrl+P` (macOS: `Cmd+P`), és írja be `task cloud-provision`.

A Visual STUDIO Code terminál egy interaktív parancssori ismerteti, hogy hozzon létre a megfelelő Azure-szolgáltatásokat:

![Felhő rendelkezés feladat](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-provision.png)

## <a name="step-3-deploy-azure-functions"></a>3. lépés Az Azure Functions üzembe helyezése

Használjon `Ctrl+P` (macOS: `Cmd+P`) futtatásához `task cloud-deploy` az Azure Functions kód telepítésére. Ez a folyamat általában 2 – 5 percig tart:

![Felhő feladatütemezés központi telepítése](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-deploy.png)

Miután az Azure-függvény sikeresen telepíti, töltse ki függvény alkalmazásnév azure_config.h fájl. Lépjen [Azure-portálon](https://portal.azure.com/) megtalálni:

![Azure-függvény Alkalmazásnév keresése](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-function.png)

> [!NOTE]
> Ha az Azure-függvény nem működik megfelelően, ellenőrizze a [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) megoldási szakasz.

## <a name="step-4-build-and-upload-the-device-code"></a>4. lépés Build, és töltse fel a kód

1. Használjon `Ctrl+P` (macOS: `Cmd+P`) futtatásához `task config-device-connection`.

2. A Terminálszolgáltatások megkérdezi, hogy a kapcsolati karakterláncot, amely lekéri a használni kívánt `task cloud-provision` lépés. Azt is megadhatja a saját eszköz kapcsolati karakterláncot, kiválasztásával **"Új létrehozása..."**

3. A Terminálszolgáltatások konfigurációs módja megadását kéri. Ehhez az szükséges, tartsa lenyomva a gombot A, majd leküldéses és kiadási a Visszaállítás gombra. A képernyőn megjelenik a DevKit azonosító és a "Konfiguráció".
  ![Ellenőrzési és a Arduino rajz feltöltése](media/iot-hub-arduino-iot-devkit-az3166-translator/config-device-connection.png)

4. Után `task config-device-connection` végzett, kattintson `F1` VS kódparancsok betölteni, és válassza ki `Arduino: Upload`, majd Visual STUDIO Code elindul, ellenőrzi, és a Arduino feltöltése vázlat: ![ellenőrzési és a Arduino rajz feltöltése](media/iot-hub-arduino-iot-devkit-az3166-translator/arduino-upload.png)

A DevKit újraindul, és elindul a kódot.

## <a name="test-the-project"></a>A projekt tesztelése

Alkalmazás inicializálása után kövesse az utasításokat a DevKit képernyőn. Az alapértelmezett adatforrás nyelvi kínai.

A fordítás egy másik nyelv kiválasztása:

1. Nyomja le az ENTER gombot A a telepítési módját adja meg.
2. Görgessen az összes támogatott forrás nyelvek B gomb megnyomásával.
3. Nyomja le az ENTER gombot A erősítse meg az adatforrás nyelvi a.
4. Nyomja le az ENTER és tartsa lenyomva a gombot B beszéd közben, majd kiadási B gomb kezdeményezheti a fordítás.
5. A lefordított szöveg angol jeleníti meg a képernyőn.

![Görgessen a nyelv kiválasztása](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Fordítási eredménye](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

A fordítási eredménye képernyőn a következőket teheti:

- Nyomja le az ENTER gombot A és B, görgessen és válassza ki a forrás.
- Nyomja le az ENTER gombot B kommunikálni, a hang küldéséhez, és a fordítási szöveg kiadás

## <a name="how-it-works"></a>Működés

![Mini-Solution-Voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

A Arduino vázlat rögzíti a hang majd visszaküldés elindítani az Azure Functions egy HTTP-kérelem. Az Azure Functions meghívja a kognitív szolgáltatás beszéd fordító API ehhez a fordítás. Miután az Azure Functions lekérdezi a fordítási szöveg, C2D üzenetet küld az eszközre. A fordítás majd a képernyőn megjelenő.

## <a name="change-device-id"></a>Módosítási eszköz azonosítója

Az alapértelmezett eszköz azonosítója regisztrálva az Azure IoT Hub **AZ3166**. Ha módosítani szeretné, hajtsa végre a [utasításokat itt](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémába ütközik, tekintse meg a [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy kapcsolatba velünk a következő csatornák:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Most elvégezte az IoT DevKit, mint a fordító Azure-függvény és kognitív szolgáltatások használatával. Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Visual Studio Code feladatütemezés segítségével automatizálhatja a felhő rendelkezések
> * Azure IoT eszköz kapcsolati karakterlánc konfigurálása
> * Az Azure-funkció telepítése
> * A szóbeli üzenet fordítási tesztelése

Továbblépés további az egyéb oktatóanyagok:

> [!div class="nextstepaction"]
> [Csatlakozás Azure IoT távoli megfigyelési megoldásgyorsító IoT DevKit AZ3166](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)
