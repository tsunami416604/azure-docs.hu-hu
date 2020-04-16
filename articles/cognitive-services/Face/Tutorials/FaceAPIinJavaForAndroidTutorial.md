---
title: 'Oktatóanyag: Arcok felismerése és bekeretezése egy képen az Android SDK használatával'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy egyszerű Android-alkalmazást hoz létre, amely a Face szolgáltatást használja a kép arcok észlelésére és keretezésére.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: dd986a7557f468b939aefe1da825c9834618047f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402915"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Oktatóanyag: Android-alkalmazás készítése képeken lévő arcok észleléséhez és bekeretezéséhez

Ebben az oktatóanyagban egy Android-alkalmazást hoz létre, amely az Azure Face szolgáltatást használja a Java SDK-n keresztül a rendszerkép emberi arcfelismeréséhez. Az alkalmazás megjeleníti a kijelölt képet, és rajzol egy keretet körül minden észlelt arc.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> - Android-alkalmazás létrehozása
> - A Face ügyféltár telepítése
> - Az ügyfélkódtár használata a képeken lévő arcok észleléséhez
> - Keret rajzolása minden észlelt arc köré

![Android-képernyőkép egy fényképről, amelyen az arcok piros téglalappal vannak bekeretezve](../Images/android_getstarted2.1.PNG)

A teljes mintakód a [Cognitive Services Face Android-tárházban](https://github.com/Azure-Samples/cognitive-services-face-android-sample) érhető el a GitHubon.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené. 

## <a name="prerequisites"></a>Előfeltételek

- Face-előfizetési kulcs. Ingyenes próba-előfizetési kulcsot a [Cognitive Services kipróbálásával](https://azure.microsoft.com/try/cognitive-services/?api=face-api)szerezhetbe. Vagy kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) című részben található utasításokat, hogy előiratkozzon a Face szolgáltatásra, és levegye a kulcsot. Ezután [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a `FACE_SUBSCRIPTION_KEY` kulcs- és szolgáltatásvégpont-karakterlánchoz, amelyet elnevezett, illetve `FACE_ENDPOINT`a.
- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/)bármely kiadása.
- [Android Studio](https://developer.android.com/studio/) API-szint 22 vagy újabb.

## <a name="create-the-android-studio-project"></a>Az Android Studio projekt létrehozása

Új Android-alkalmazásprojekt létrehozásához kövesse az alábbi lépéseket.

1. Az Android Studio alkalmazásban válassza **az Új Android Studio-projekt indítása**lehetőséget.
1. A **Create Android Project** (Android-projekt létrehozása) képernyőn szükség esetén módosítsa az alapértelmezett mezőket, majd kattintson a **Next** (Tovább) gombra.
1. Az **Android-eszközök célzása** képernyőn a legördülő választóval válassza az **API 22** vagy újabb verzióját, majd kattintson a **Tovább**gombra.
1. Válassza az **Empty Activity** (Üres tevékenység) elemet, majd kattintson a **Next** (Tovább) gombra.
1. Törölje a **Backwards Compatibility** (Visszamenőleges kompatibilitás) beállítás bejelölését, majd kattintson a **Finish** (Befejezés) gombra.

## <a name="add-the-initial-code"></a>Kezdeti kód hozzáadása

### <a name="create-the-ui"></a>A felhasználói felület létrehozása

Nyissa *meg activity_main.xml fájlban*. Az Elrendezésszerkesztőben jelölje ki a **Szöveg** lapot, majd cserélje le a tartalmat a következő kódra.

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/res/layout/activity_main.xml?name=snippet_activitymain)]

### <a name="create-the-main-class"></a>A főosztály létrehozása

Nyissa meg *a MainActivity.java-t,* és cserélje le a meglévő `import` állításokat a következő kódra.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_imports)]

Ezután cserélje le a **MainActivity** osztály tartalmát a következő kódra. Ezzel létrehoz egy eseménykezelőt a **gombon,** amely új tevékenységet indít, hogy a felhasználó kijelöljön egy képet. A képet az **ImageView nézetben**jeleníti meg.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_methods)]

### <a name="try-the-app"></a>Próbálja ki az alkalmazást

Megjegyzés ki a hívást **detectAndFrame** az **onActivityResult** metódusban. Ezután nyomja le a **Futtatás** gombot a menüben az alkalmazás teszteléséhez. Amikor az alkalmazás megnyílik egy emulátorban vagy egy csatlakoztatott eszközön, kattintson a **Tallózás** gombra az alján. Meg kell jelennie az eszköz fájlkijelölési párbeszédpaneljének. Válasszon ki egy képet, és ellenőrizze, hogy az megjelenik-e az ablakban. Ezután zárja be az alkalmazást, és lépjen tovább a következő lépésre.

![Android-képernyőkép egy fényképről, amelyen arcok láthatóak](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>A Face SDK hozzáadása

### <a name="add-the-gradle-dependency"></a>A Gradle-függőség hozzáadása

A **Project** (Projekt) panelen válassza az **Android** elemet a legördülő listából. Bontsa ki a **Gradle Scripts** (Gradle-szkriptek) elemet, majd nyissa meg a *build.gradle (Module: app)* elemet. Adjon hozzá egy függőséget a `com.microsoft.projectoxford:face:1.4.3` arcfelismerési ügyfélkódtárhoz, ahogyan az az alábbi képernyőképen látható, majd kattintson a **Sync Now** (Szinkronizálás most) gombra.

![Android Studio-képernyőkép az alkalmazás build.gradle fájljáról](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>Az Arccal kapcsolatos projektkód hozzáadása

Lépjen vissza a **MainActivity.java-ra,** és adja hozzá a következő `import` állításokat:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_face_imports)]

Ezután szúrja be a következő kódot a **MainActivity** osztályba, az **onCreate** metódus fölé:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_fields)]

A **Project** (Projekt) panelen bontsa ki az **app** elemet, majd a **manifests** elemet, és nyissa meg az *AndroidManifest.xml* fájlt. Illessze be a következő elemet a `manifest` elem közvetlen gyermekeként:

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/AndroidManifest.xml?name=snippet_manifest_entry)]

## <a name="upload-image-and-detect-faces"></a>Kép feltöltése és arcok észlelése

Az alkalmazás észleli az arcokat a **faceClient.Face.DetectWithStreamAsync** metódus hívásával, amely beburkolja a [REST-észlelési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API-t, és visszaadja a **Face-példányok** listáját.

Minden visszaadott **arc** tartalmaz egy téglalapot, amely jelzi a helyét, és egy sor választható arcattribútumot tartalmaz. Ebben a példában csak az arc téglalapok kért.

Szúrja be a következő két módszert a **MainActivity** osztályba. Amikor az arcfelismerés befejeződik, az alkalmazás meghívja a **drawFaceRectanglesOnBitmap metódust** az **ImageView**módosításához. Ezt a módszert fogja meghatározni legközelebb.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_detection_methods)]

## <a name="draw-face-rectangles"></a>Arctéglalapok rajzolása

Szúrja be a következő segítő módszert a **MainActivity** osztályba. Ez a módszer egy téglalapot rajzol az egyes észlelt lap köré, az egyes **arcpéldányok** téglalapkoordinátáit használva.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_drawrectangles)]

Végül vond vissza a hívást az **onActivityResult** **detectAndFrame** metódusához.

## <a name="run-the-app"></a>Az alkalmazás futtatása

Futtassa az alkalmazást, és keressen egy képet, amelyen egy arc látható. Várjon néhány másodpercet, amíg az arcfelismerési szolgáltatás válaszol. A képen lévő lapokon piros téglalapjelenik meg.

![Android screenshot arcok piros téglalapok rajzolt körülöttük](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja a Face Java SDK-t egy alkalmazás létrehozásához a kép arcok észlelésére és keretezésére. Ezután tudjon meg többet az arcfelismerés részleteiről.

> [!div class="nextstepaction"]
> [Arcok észlelése egy képen](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
