---
title: 'Gyors útmutató: Ismeri fel a beszéd, a Java, a Cognitive Services beszédfelismerő SDK használatával Android rendszeren'
titleSuffix: Microsoft Cognitive Services
description: Ismerje meg, hogyan beszédfelismerést javában Android a Cognitive Services beszédfelismerő SDK használatával
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: fmegen
ms.openlocfilehash: ad1bf15f198529487dbed92d8f8639704b0c5fc7
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325125"
---
# <a name="quickstart-recognize-speech-in-java-on-android-using-the-speech-sdk"></a>Gyors útmutató: Beszédfelismerést Java nyelven a Speech SDK használatával Android rendszeren

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebben a cikkben megismerheti, hogyan hozhat létre egy Java-alkalmazást az Android a Cognitive Services beszédfelismerő SDK a beszédfelismerés lefényképezze lesz.
Az alkalmazás a Microsoft Cognitive Services beszédfelismerő SDK Maven csomag, a 0.5.0-s verzió és az Android Studio 3.1 alapul.

> [!NOTE]
> A beszédfelismerés Devices SDK-val és a Roobo eszköz, keresse fel a [Speech Devices SDK-val](speech-devices-sdk.md) lapot.

## <a name="prerequisites"></a>Előfeltételek

* A beszédfelismerési szolgáltatás egy előfizetési kulcsot. Lásd: [próbálja ki ingyenesen a speech service](get-started.md).
* Számítógép (Windows, Linux, Mac) kompatibilis Android Studio futtatásához.
* 3.1-es verziójának [Android Studio](https://developer.android.com/studio/).
* Egy ARM-alapú Android-eszköz (API 23: Android 6.0-s Marshmallow vagy újabb) [engedélyezve van, fejlesztési](https://developer.android.com/studio/debug/dev-options) működő mikrofonnal.

## <a name="create-an-android-studio-project"></a>Az Android Studio-projekt létrehozása

Indítsa el az Android Studióban, és válassza a **indítsa el az új Android Studio-projekt**.

![](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

Az a **új projekt létrehozása** varázsló, amely akár, válassza a következőket:

1. Az a **Android-projekt létrehozása** képernyőn írja be **rövid** , **alkalmazásnév**, **samples.speech.cognitiveservices.microsoft.com** , **vállalati tartomány**, és válassza ki a projekt helyére. Ne jelölje be a jelölőnégyzeteket, majd kattintson **tovább**.

   ![](media/sdk/qs-java-android-02-create-android-project.png)

1. Az a **Target Android-eszközök** képernyőn ellenőrizze **telefon és táblagép** az egyetlen lehetőség, válassza **API 23: Android 6.0 (marshmallow rendszert)** alatt, és kattintson a legördülő listából **Tovább**.

   ![](media/sdk/qs-java-android-03-target-android-devices.png)

1. Az a **tevékenység hozzáadása Mobile** képernyőn válassza ki **üres tevékenység** kattintson **tovább**.

   ![](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. Az a **konfigurálása tevékenység** képernyőn, használja **MainActivity** a tevékenység nevét, és **tevékenység\_fő** elrendezés nevet. Ellenőrizze a különül, és kattintson a **Befejezés**.

   ![](media/sdk/qs-java-android-05-configure-activity.png)

Futtatás ideje után kell az újonnan létrehozott Android Studio project merülnek fel.

## <a name="configure-your-project-for-the-speech-sdk"></a>A beszédfelismerés SDK projekt konfigurálásához

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A Cognitive Services beszédfelismerő SDK jelenlegi verziója `0.5.0`.

A beszédfelismerés SDK for Android van csomagolva, mint egy [AAR (Androidos függvénytár)](https://developer.android.com/studio/projects/android-library), amely tartalmazza a szükséges kódtárak, valamint a szükséges Android-engedélyek útmutatójához.
A Maven adattárban található https://csspeechstorage.blob.core.windows.net/maven/.

Beállítása a projekthez, a beszéd SDK alább ismertetünk.

Nyissa meg a projekt alatt ablakában **fájl** \> **Projektstruktúra**.
Az ablakban, hogy ellenőrizze a következő módosításokat (kattintson **OK** csak azután hajtsa végre a lépéseket):

1. Válassza ki **projekt**, és szerkesztheti a **alapértelmezett erőforrástár-tárház** beállításokat, amelyeket egy vessző és a Maven adattár URL-címe aposztrófok közé `'https://csspeechstorage.blob.core.windows.net/maven/'`:

  ![](media/sdk/qs-java-android-06-add-maven-repository.png)

1. Továbbra is ugyanazon a képernyőn, a bal oldalon válassza ki a **alkalmazás** modult, és a felső a **függőségek** fülre. Ezután kattintson a zöld plusz jelre a jobb felső sarokban, és válassza ki **könyvtár függőségi**.

  ![](media/sdk/qs-java-android-07-add-module-dependency.png)

1. Az ablakban, hogy írja be a nevét és verzióját, a beszéd SDK Android- `com.microsoft.cognitiveservices.speech:client-sdk:0.5.0`, majd kattintson a **OK**.
   A beszédfelismerés SDK szeretné adni a függőségek listáját, alább látható módon:

  ![](media/sdk/qs-java-android-08-dependency-added.png)

1. A képernyő felső részén válassza ki a **tulajdonságok** fülre. Válassza ki **1.8-as** egyaránt **forrás kompatibilitási** és **cél kompatibilitási**.

  ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Végül kattintson **OK** gombra kattintva zárja be a **Projektstruktúra** windows és minden frissítés alkalmazása.

## <a name="create-a-minimal-ui"></a>Minimális felhasználói felület létrehozása

A fő tevékenységnél, az elrendezést szerkesztése `activity_main.xml`.
Alapértelmezés szerint azt kell kapja meg az alkalmazás nevével és a egy "Hello World!" kezdetű TextView címsor.

* Kattintson a TextView. A jobb felső sarokban lévő ID attribútum `hello`.

* A bal felső sarkában a palettán a `activity_main.xml` ablakban, húzzon egy gombot a szöveg fölé a üres terén.

* A gomb attribútumok a jobb oldali értékét a `onClick` attribútumot, adja meg `onSpeechButtonClicked`, amely a gomb kezelő neve lesz.
  A jobb felső sarokban lévő ID attribútum `button`.

* Használja a designer tetején lévő magic varázspálcaikonra, ha azt szeretné, elrendezés megkötések kikövetkeztetnünk az Ön számára.

  ![](media/sdk/qs-java-android-10-infer-layout-constraints.png)

A szöveg és a felhasználói felületének grafikus verziója kell kinéznie ehhez hasonló:

<table>
<tr>
<td valign="top">
![](media/sdk/qs-java-android-11-gui.png)
</td>
<td valign="top">
[! code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]
</td>
</tr>
</table>

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Szerkessze a `MainActivity.java` fájl adatforrásra, és cserélje le a kódok (alább a csomag utasítás) a következőket:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * A `onCreate` metódus tartalmazza a kódot, amely a mikrofon és internetes engedélyeket kér, valamint a inicializálja a platformot natív kötést. A natív platform kötéseket konfigurálása csak egyszer szükséges, hogy el kell végezni korai alkalmazás inicializálása során.
   
   * A metódus `onSpeechButtonClicked` lett korábban már eleve úgy van kialakítva, a gombra való kattintással kezelő. Egy gomb megnyomása elindítja a tényleges beszédfelismerés.

1. Cserélje le a karakterláncot `YourSubscriptionKey` az előfizetési kulccsal végzett.

1. Cserélje le a karakterláncot `YourServiceRegion` együtt a [régió](regions.md) az előfizetéséhez tartozó (például `westus` az ingyenes próba-előfizetésre).

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

* Hozhat létre, nyomja le a Ctrl + F9, vagy válasszon **összeállítása** \> **projekt győződjön meg arról,**.

* Az Android-eszköz csatlakozhat a fejlesztési számítógép. Ellenőrizze, hogy [fejlesztői mód és az USB-hibakeresés engedélyezve](https://developer.android.com/studio/debug/dev-options).

* Indítsa el az alkalmazást, nyomja le a Shift + F10, vagy válassza ki **futtatása** \> **"alkalmazás" futtatása**.

* A központi telepítési cél Windows, hogy válassza ki az Android-eszközön.

  ![Indítsa el az alkalmazást into hibakeresés](media/sdk/qs-java-android-12-deploy.png)

* Az alkalmazás az eszközön kell elindítani.
  Ha megnyomja a gombot, a következő 15 másodperc elfogadtatni és a felhasználói felületén látható (kell jelennie a válasz az Android studióban logcat ablakban láthatók):

  ![Felhasználói felület követően sikeres felismerése](media/sdk/qs-java-android-13-gui-on-device.png)

Ezen a képernyőfelvételen látható az Android a rövid útmutató azt állapítja meg. A teljes projekt mintakód a minta tárház letölthető.

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Keresse meg az ehhez a mintához a `quickstart/java-android` mappát.

## <a name="next-steps"></a>További lépések

* [A minták letöltése](speech-sdk.md#get-the-samples)
