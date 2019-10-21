---
title: 'Gyors útmutató: beszédfelismerés felismerése, Java (Android) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ismerheti fel a beszédfelismerést Java nyelven Androidon a Speech SDK használatával
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 2fb7bc0851868f6551ed57d742c4005550caec99
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675583"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Rövid útmutató: Beszéd felismerése Androidon, Java nyelven a Speech SDK segítségével

A rövid útmutatók a [beszédfelismeréshez](quickstart-text-to-speech-java-android.md) és a [hang-első virtuális asszisztenshez](quickstart-virtual-assistant-java-android.md)is elérhetők.

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebből a cikkből megtudhatja, hogyan fejlesztheti az Androidhoz készült Java-alkalmazásokat az Azure Cognitive Services Speech SDK-val a beszéd szövegbe való átírásához.

Az alkalmazás a Speech SDK Maven-csomagon alapul, és Android Studio 3,3. A Speech SDK jelenleg kompatibilis a 32 bites vagy 64 bites ARM-és Intel x86/x64-kompatibilis processzorokkal rendelkező Android-eszközökkel.

> [!NOTE]
> A Speech Devices SDK-ról és a Roobo eszközről lásd: [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez szüksége lesz egy Speech Services-előfizetési kulcsra. amelyet ingyenesen is beszerezhet. További információ: [a Speech Services ingyenes kipróbálása](get-started.md).

## <a name="create-and-configure-a-project"></a>Projekt létrehozása és konfigurálása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-a-user-interface"></a>Felhasználói felület létrehozása

Most létrehozunk egy alapszintű felhasználói felületet az alkalmazáshoz. Kezdje el szerkeszteni a fő tevékenység (`activity_main.xml`) elrendezését. Kezdetben az elrendezés tartalmaz egy címsort az alkalmazás nevével, valamint egy TextView, amely tartalmazza a következő szöveget: ""Helló világ!"alkalmazás!".

* Válassza ki a TextView elemet. Módosítsa a jobb felső sarokban található ID (azonosító) attribútumot a következőre: `hello`.

* A `activity_main.xml` ablak bal felső részén található palettáról húzzon egy gombot a szöveg feletti üres helyre.

* A gomb attribútumai jobb oldalon láthatók. Az `onClick` attribútum értékét állítsa a következőre: `onSpeechButtonClicked`. Ezen a néven írni fogunk később egy metódust, amely kezelni fogja a gombeseményt. Módosítsa a jobb felső sarokban található ID (azonosító) attribútumot a következőre: `button`.

* A tervező felső részén található varázspálca ikonra kattintva kikövetkeztetheti az elrendezés korlátozásait.

  ![A varázspálca ikon képernyőképe](media/sdk/qs-java-android-10-infer-layout-constraints.png)

A felhasználói felület szöveg-és grafikus ábrázolásának ekkor a következőképpen kell kinéznie:

![Felhasználói felület](media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a `MainActivity.java` forrásfájlt. Cserélje le a fájl összes kódját a következőre:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Az `onCreate` metódus egy olyan kódot tartalmaz, amely mikrofon- és internet-engedélyeket kér, majd elindítja a natív platform kötését. A natív platform kötését csak egyszer kell elvégezni. Erre érdemes mihamarabb, már az alkalmazás inicializálása során sort keríteni.

   * Ahogy korábban már említettük, az `onSpeechButtonClicked` metódus kezeli azt, ha a gombra kattintanak. Egy gomb megnyomásával elindíthatja a beszédfelismerési szöveg átírását.

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` karakterláncot az előfizetéséhez társított [régióra](regions.md) is. Használja például a `westus` értéket az ingyenes próbaverziós előfizetéshez.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Csatlakoztassa az Android-eszközt a fejlesztői számítógéphez. Győződjön meg arról, hogy engedélyezve van a [fejlesztési mód és az USB-hibakeresés](https://developer.android.com/studio/debug/dev-options) az eszközön.

1. Az alkalmazás létrehozásához válassza a CTRL + F9 billentyűkombinációt, vagy válassza a **létrehozás**  >  a**projekt** létrehozása lehetőséget a menüsávon.

1. Az alkalmazás indításához válassza a SHIFT + F10 lehetőséget, vagy válassza a **futtatás**  > **Futtatás az "app"** lehetőséget.

1. A megjelenő központi telepítési cél ablakban válassza ki az Android-eszközét.

   ![A Select Deployment Target (Üzembehelyezési cél kiválasztása) ablak képernyőképe](media/sdk/qs-java-android-12-deploy.png)

A beszédfelismerési szakasz elindításához kattintson az alkalmazásban a gombra. A rendszer a következő 15 másodperces angol nyelvű beszédet küldi el a Speech Servicesnek és az átiratoknak. Az eredmény az Android-alkalmazásban és az Android Studio logcat ablakában jelenik meg.

![Az Android-alkalmazás képernyőképe](media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A Java-minták megismerése a GitHubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még:

- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
