---
title: 'Oktatóanyag: Arcok felismerése és bekeretezése egy képen az Android SDK használatával'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban létrehozhat egy egyszerű Android-alkalmazást, amely a Face API-t használja és keret arcok a képen.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 11/12/2018
ms.author: pafarley
ms.openlocfilehash: 4378d04d8909ecb0cd77c3196b74ecd51eb19638
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686228"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Oktatóanyag: Android-alkalmazás készítése képeken lévő arcok észleléséhez és bekeretezéséhez

Ebben az oktatóanyagban létrehozhat egy egyszerű Android-alkalmazás, amely az Azure Face API, a Java SDK-n keresztül az emberi arcok észlelése a képet. Az alkalmazás megjeleníti a kiválasztott kép, és megrajzolja az egyes észlelt face köré keretet.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> - Android-alkalmazás létrehozása
> - A Face API-ügyfélkódtárát telepítése
> - Az ügyfélkódtár használata a képeken lévő arcok észleléséhez
> - Keret rajzolása minden észlelt arc köré

![Android-képernyőkép egy fényképről, amelyen az arcok piros téglalappal vannak bekeretezve](../Images/android_getstarted2.1.PNG)

A teljes minta kódja megtalálható a [Cognitive Services Face Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample) tárházban a Githubon.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek

- A Face API előfizetési kulcs. Megjelenik a származó ingyenes próba-előfizetését kulcsok [próbálja meg a Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Másik lehetőségként kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Face API szolgáltatás és a kulcs beszerzése.
- [Az Android Studio](https://developer.android.com/studio/) 22 vagy annál újabb API-szintű (a Face ügyféloldali kódtár által igényelt).

## <a name="create-the-android-studio-project"></a>Az Android Studio-projekt létrehozása

Kövesse az alábbi lépéseket egy új Android-alkalmazás projekt létrehozásához.

1. Az Android Studióban válassza a **indítsa el az új Android Studio-projekt**.
1. A **Create Android Project** (Android-projekt létrehozása) képernyőn szükség esetén módosítsa az alapértelmezett mezőket, majd kattintson a **Next** (Tovább) gombra.
1. Az a **Target Android-eszközök** képernyő, válassza ki a legördülő listából választómezőt használja **API 22-es** vagy újabb, majd kattintson **tovább**.
1. Válassza az **Empty Activity** (Üres tevékenység) elemet, majd kattintson a **Next** (Tovább) gombra.
1. Törölje a **Backwards Compatibility** (Visszamenőleges kompatibilitás) beállítás bejelölését, majd kattintson a **Finish** (Befejezés) gombra.

## <a name="add-the-initial-code"></a>Kezdeti kód hozzáadása

### <a name="create-the-ui"></a>A felhasználói felület létrehozása

Nyissa meg *activity_main.xml*. Az elrendezés Editorban válassza a **szöveg** lapfülre, majd cserélje ki annak tartalmát az alábbira.

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/res/layout/activity_main.xml?range=1-18)]

### <a name="create-the-main-class"></a>Hozzon létre a fő osztályban

Nyissa meg *MainActivity.java* , és cserélje le a meglévő `import` utasítások a következő kóddal.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=3-11)]

Ezután cserélje le a tartalmát a **MainActivity** osztályban az alábbi kódra. Ez létrehoz egy eseménykezelőt a **gomb** , amely elindítja a új tevékenység, amely engedélyezi a felhasználó számára, válasszon ki egy képet. Megjeleníti a képet a a **ImageView**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=29-68)]

### <a name="try-the-app"></a>Próbálja ki az alkalmazást

Tegye megjegyzésbe a hívást **detectAndFrame** a a **onActivityResult** metódust. Nyomja le az **futtatása** a menü az alkalmazás teszteléséhez. Ha az alkalmazás megnyílik egy emulátort vagy egy csatlakoztatott eszközt, kattintson a **Tallózás** alján. Az eszköz Fájlkiválasztási párbeszédpanelt kell megjelennie. Kép kiválasztása, és győződjön meg arról, hogy az ablakban megjelenik. Ezt követően zárja be az alkalmazást, és folytassa a következő lépéssel.

![Android-képernyőkép egy fényképről, amelyen arcok láthatóak](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>A Face SDK hozzáadása

### <a name="add-the-gradle-dependency"></a>A Gradle-függőség hozzáadása

A **Project** (Projekt) panelen válassza az **Android** elemet a legördülő listából. Bontsa ki a **Gradle Scripts** (Gradle-szkriptek) elemet, majd nyissa meg a *build.gradle (Module: app)* elemet. Adjon hozzá egy függőséget a `com.microsoft.projectoxford:face:1.4.3` arcfelismerési ügyfélkódtárhoz, ahogyan az az alábbi képernyőképen látható, majd kattintson a **Sync Now** (Szinkronizálás most) gombra.

![Android Studio-képernyőkép az alkalmazás build.gradle fájljáról](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>A Face kapcsolódó Projektkód hozzáadása

Lépjen vissza a **MainActivity.java** , és adja hozzá a következő `import` utasításokat:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=13-14)]

Ezután helyezze be a következő kódot a **MainActivity** osztályhoz, fent a **onCreate** metódus:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=17-27)]

Le kell cserélnie `<Subscription Key>` az előfizetési kulccsal végzett. Továbbá cserélje le `<API endpoint>` -a a Face API végpontra, a megfelelő régióazonosító használata a kulcshoz. Ingyenes próba-előfizetését kulcsokat hoz létre a a **westus** régióban. Egy példa API-végpont értékét a következő lesz:

```java
apiEndpoint = "https://westus.api.cognitive.microsoft.com/face/v1.0";
```

A **Project** (Projekt) panelen bontsa ki az **app** elemet, majd a **manifests** elemet, és nyissa meg az *AndroidManifest.xml* fájlt. Illessze be a következő elemet a `manifest` elem közvetlen gyermekeként:

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/AndroidManifest.xml?range=5)]

## <a name="upload-image-and-detect-faces"></a>Arcok észlelése és a Rendszerkép feltöltése

Az alkalmazás meghívásával arcokat észleli a **FaceServiceClient.detect** metódussal, amely becsomagolja a [hibakeresés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API-t és a egy listáját adja vissza **Face** példányok.

Minden egyes visszaküldött **Face** egy téglalapot elfoglalt helye, több választható arctulajdonságok kombinált tartalmazza. Ebben a példában csak a arcjelző négyszögek kérik.

Helyezze be az alábbi két módszer a **MainActivity** osztály. Vegye figyelembe, hogy arcfelismerés befejezését követően az alkalmazás meghívja a **drawFaceRectanglesOnBitmap** úgy módosíthatók a **ImageView**. Ez a metódus ezután fogja meghatározni.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=70-150)]

## <a name="draw-face-rectangles"></a>Arcjelző négyszögek rajzolása

Az alábbi segédmetódus beszúrása a **MainActivity** osztály. Ez a módszer minden észlelt lapot, az egyes a téglalap koordináták segítségével körül négyszögbe **Face** példány.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=152-173)]

Végezetül állítsa vissza a hívást a **detectAndFrame** metódus az **onActivityResult**.

## <a name="run-the-app"></a>Az alkalmazás futtatása

Futtassa az alkalmazást, és keressen egy képet, amelyen egy arc látható. Várjon néhány másodpercet, amíg az arcfelismerési szolgáltatás válaszol. Az egyes az arcok a képen piros téglalap megjelennie.

![Arcok körül őket piros téglalapokkal Android képernyőképe](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte az alapvető folyamat a Face API a Java SDK-val, és létrehozott egy alkalmazást, és alkalmas keretet biztosítanak az arcok a képen. Ezután további információ az arcfelismerés részleteit.

> [!div class="nextstepaction"]
> [Arcok észlelése egy képen](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)