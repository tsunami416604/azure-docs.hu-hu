---
title: 'Rövid útmutató: Beszédfelismerés mikrofonból, Java (Android) – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ismerheti fel a beszédbeszédet Java-ban Android on a Speech SDK használatával
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/05/2019
ms.author: wolfma
ms.openlocfilehash: c37a52ea2a7d5f1d3e325c2f6d17bc075f50c5e0
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671492"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene járni:

> [!div class="checklist"]
> * [Azure-beszédfelismerési erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztői környezet beállítása](../../../../quickstarts/setup-platform.md?tabs=android&pivots=programming-language-java)
> * Győződjön meg arról, hogy rendelkezik a mikrofonhoz a hangrögzítéshez

## <a name="create-a-user-interface"></a>Felhasználói felület létrehozása

Most létrehozunk egy alapvető felhasználói felületet az alkalmazáshoz. Kezdje el szerkeszteni a fő tevékenység (`activity_main.xml`) elrendezését. Kezdetben az elrendezés tartalmaz egy címsort az alkalmazás nevével, és egy TextView nézetet, amely a "Hello World!" szöveget tartalmazza.

* Jelölje ki a TextView elemet. Módosítsa a jobb felső sarokban található ID (azonosító) attribútumot a következőre: `hello`.

* Az `activity_main.xml` ablak bal felső részén lévő palettáról húzzon egy gombot a szöveg feletti üres területre.

* A gomb attribútumai jobb oldalon láthatók. Az `onClick` attribútum értékét állítsa a következőre: `onSpeechButtonClicked`. Ezen a néven írni fogunk később egy metódust, amely kezelni fogja a gombeseményt. Módosítsa a jobb felső sarokban található ID (azonosító) attribútumot a következőre: `button`.

* A tervező felső részén található varázspálca ikonra kattintva kikövetkeztetheti az elrendezés korlátozásait.

  ![A varázspálca ikon képernyőképe](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

A felhasználói felület szövegének és grafikus ábrázolásának így kell kinéznie:

![Felhasználói felület](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a `MainActivity.java` forrásfájlt. Cserélje le a fájl összes kódját a következőkre:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Az `onCreate` metódus egy olyan kódot tartalmaz, amely mikrofon- és internet-engedélyeket kér, majd elindítja a natív platform kötését. A natív platform kötését csak egyszer kell elvégezni. Erre érdemes mihamarabb, már az alkalmazás inicializálása során sort keríteni.

   * Ahogy korábban már említettük, az `onSpeechButtonClicked` metódus kezeli azt, ha a gombra kattintanak. Egy gombnyomással váltja elő a beszéd-szöveg átírást.

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le `YourServiceRegion` a karakterláncot az előfizetéshez társított [régió](https://aka.ms/speech/sdkregion) **régiórégiórégióból** származó régióra. Például használja `westus` az ingyenes próba-előfizetés.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Csatlakoztassa az Android-eszközt a fejlesztői számítógéphez. Győződjön meg arról, hogy engedélyezte a fejlesztési módot és az [USB-hibakeresést](https://developer.android.com/studio/debug/dev-options) az eszközön.

1. Az alkalmazás létrehozásához válassza a Ctrl+F9 lehetőséget, vagy válassza a menüsor > **Project** **összeállítása**lehetőséget.

1. Az alkalmazás elindításához válassza a Shift+F10 vagy a Run**Run 'app' (Alkalmazás futtatása)** **Run** > lehetőséget.

1. A megjelenő központi telepítési célablakban válassza ki az Android-eszközt.

   ![A Select Deployment Target (Üzembehelyezési cél kiválasztása) ablak képernyőképe](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

A beszédfelismerési szakasz megkezdéséhez jelölje ki az alkalmazás gombját. A következő 15 másodpercben kimondott (angol nyelvű) beszédet a rendszer elküldi a Speech Service-nek, amely írott szöveggé alakítja. Az eredmény az Android-alkalmazásban és az Android Studio logcat ablakában jelenik meg.

![Az Android-alkalmazás képernyőképe](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]

