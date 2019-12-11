---
title: 'Oktatóanyag: Arcok felismerése és bekeretezése egy képen az Android SDK használatával'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban létre fog hozni egy egyszerű Android-alkalmazást, amely a Face API segítségével felismeri és képkockát készít a képekben.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: ce0b308077505d5af1d757f1684c50505b11831e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977794"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Oktatóanyag: Android-alkalmazás készítése képeken lévő arcok észleléséhez és bekeretezéséhez

Ebben az oktatóanyagban létre fog hozni egy Android-alkalmazást, amely az Azure Face API-t használja a Java SDK-n keresztül, hogy felismerje az emberi arcokat a képen. Az alkalmazás megjeleníti a kiválasztott képet, és egy keretet rajzol az észlelt arcok köré.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> - Android-alkalmazás létrehozása
> - Az Face API ügyféloldali kódtár telepítése
> - Az ügyfélkódtár használata a képeken lévő arcok észleléséhez
> - Keret rajzolása minden észlelt arc köré

![Android-képernyőkép egy fényképről, amelyen az arcok piros téglalappal vannak bekeretezve](../Images/android_getstarted2.1.PNG)

A teljes mintakód a GitHubon a [Cognitive Services Face Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample) -tárházban érhető el.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek

- Egy Face API előfizetési kulcs. A [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api)ingyenes próbaverziós előfizetési kulcsot is kaphat. Vagy kövesse a [Cognitive Services fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) az Face API szolgáltatásra való előfizetéshez és a kulcs beszerzéséhez című témakör utasításait. Ezután [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a kulcs és szolgáltatás végponti karakterláncához, `FACE_SUBSCRIPTION_KEY` és `FACE_ENDPOINT`néven.
- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.
- [Android Studio](https://developer.android.com/studio/) a 22-es vagy újabb API-szinttel (az arc ügyféloldali kódtár számára szükséges).

## <a name="create-the-android-studio-project"></a>A Android Studio projekt létrehozása

Kövesse az alábbi lépéseket egy új Android-alkalmazás projekt létrehozásához.

1. A Android Studio területen válassza az **új Android Studio projekt indítása**lehetőséget.
1. A **Create Android Project** (Android-projekt létrehozása) képernyőn szükség esetén módosítsa az alapértelmezett mezőket, majd kattintson a **Next** (Tovább) gombra.
1. A **cél Android-eszközök** képernyőn a legördülő listából válassza ki az **API 22** -es vagy újabb verzióját, majd kattintson a **tovább**gombra.
1. Válassza az **Empty Activity** (Üres tevékenység) elemet, majd kattintson a **Next** (Tovább) gombra.
1. Törölje a **Backwards Compatibility** (Visszamenőleges kompatibilitás) beállítás bejelölését, majd kattintson a **Finish** (Befejezés) gombra.

## <a name="add-the-initial-code"></a>Kezdeti kód hozzáadása

### <a name="create-the-ui"></a>A felhasználói felület létrehozása

Nyissa meg *activity_main. xml fájlt*. Az elrendezés-szerkesztőben válassza a **text (szöveg** ) fület, majd cserélje le a tartalmát a következő kódra.

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/res/layout/activity_main.xml?name=snippet_activitymain)]

### <a name="create-the-main-class"></a>A Main osztály létrehozása

Nyissa meg a *MainActivity. Java* programot, és cserélje le a meglévő `import` utasításokat a következő kódra.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_imports)]

Ezután cserélje le a **MainActivity** osztály tartalmát a következő kódra. Ez létrehoz egy eseménykezelőt a **gombon** , amely egy új tevékenység elindításával engedélyezi a felhasználó számára egy kép kiválasztását. Megjeleníti a képet a **ImageView**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_methods)]

### <a name="try-the-app"></a>Próbálja ki az alkalmazást

Megjegyzés a **onActivityResult** metódusban található **detectAndFrame** -híváshoz. Ezután nyomja meg a **Futtatás** gombot a menüben az alkalmazás teszteléséhez. Amikor az alkalmazás megnyílik, vagy egy emulátorban vagy egy csatlakoztatott eszközön, kattintson a lenti **Tallózás** gombra. Ekkor megjelenik az eszköz fájljának kiválasztására szolgáló párbeszédpanel. Válasszon ki egy rendszerképet, és ellenőrizze, hogy megjelenik-e az ablakban. Ezután zárjuk be az alkalmazást, és folytassa a következő lépéssel.

![Android-képernyőkép egy fényképről, amelyen arcok láthatóak](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>A Face SDK hozzáadása

### <a name="add-the-gradle-dependency"></a>A Gradle függőség hozzáadása

A **Project** (Projekt) panelen válassza az **Android** elemet a legördülő listából. Bontsa ki a **Gradle Scripts** (Gradle-szkriptek) elemet, majd nyissa meg a *build.gradle (Module: app)* elemet. Adjon hozzá egy függőséget a `com.microsoft.projectoxford:face:1.4.3` arcfelismerési ügyfélkódtárhoz, ahogyan az az alábbi képernyőképen látható, majd kattintson a **Sync Now** (Szinkronizálás most) gombra.

![Android Studio-képernyőkép az alkalmazás build.gradle fájljáról](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>A Face kapcsolódó projekt kódjának hozzáadása

Lépjen vissza a **MainActivity. Java** -hoz, és adja hozzá a következő `import` utasításokat:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_face_imports)]

Ezután szúrja be a következő kódot a **MainActivity** osztályban a **onCreate** metódus felett:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_fields)]

A **Project** (Projekt) panelen bontsa ki az **app** elemet, majd a **manifests** elemet, és nyissa meg az *AndroidManifest.xml* fájlt. Illessze be a következő elemet a `manifest` elem közvetlen gyermekeként:

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/AndroidManifest.xml?name=snippet_manifest_entry)]

## <a name="upload-image-and-detect-faces"></a>Rendszerkép feltöltése és arcok észlelése

Az alkalmazás az **faceClient. Face. DetectWithStreamAsync** metódus meghívásával fogja felderíteni az arcokat, amely az [észlelési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API, és a **Face** instances listáját adja vissza.

Minden visszaadott **arc** tartalmaz egy téglalapot, amely jelzi a helyét, és egy sor opcionális arc-attribútummal együtt. Ebben a példában csak az arc téglalapokat kéri a rendszer.

Szúrja be az alábbi két metódust a **MainActivity** osztályba. Az Arcfelismerés befejeződése után az alkalmazás meghívja a **drawFaceRectanglesOnBitmap** metódust a **ImageView**módosításához. Ezt a metódust a következő módon fogja meghatározni.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_detection_methods)]

## <a name="draw-face-rectangles"></a>Face téglalapok rajzolása

Szúrja be a következő Helper metódust a **MainActivity** osztályba. Ez a metódus egy téglalapot rajzol az összes észlelt arc körül, az egyes **Faces** -példányok téglalap koordinátáinak használatával.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_drawrectangles)]

Végül a **OnActivityResult** **detectAndFrame** metódusának meghívása megjegyzéssel.

## <a name="run-the-app"></a>Az alkalmazás futtatása

Futtassa az alkalmazást, és keressen egy képet, amelyen egy arc látható. Várjon néhány másodpercet, amíg az arcfelismerési szolgáltatás válaszol. A képen egy piros négyszögnek kell megjelennie.

![Androidos képernyőkép az arcokról, piros téglalapokkal rajzolva](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte a Face API Java SDK használatának alapszintű folyamatát, és létrehozott egy alkalmazást az arcok észleléséhez és a képek keretének megjelenítéséhez. Következő lépésként tekintse meg a Arcfelismerés részletes adatait.

> [!div class="nextstepaction"]
> [Arcok észlelése egy képen](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
