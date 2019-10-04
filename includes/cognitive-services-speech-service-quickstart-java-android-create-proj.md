---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 5/23/2019
ms.author: travisw
ms.openlocfilehash: a8118d80e85d562fa4137ed1f1844e6bf9f1793e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485036"
---
1. Indítsa el az Android Studio alkalmazást, majd az üdvözlőablakban válassza a **Start a new Android Studio project** (Új Android Studio-projekt indítása) elemet.

    ![Az Android Studio üdvözlőablakának képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. A **válassza ki a projekt** varázsló jelenik meg, válasszon **telefon és táblagép** és **üres tevékenység** tevékenység kiválasztási mezőben. Kattintson a **Tovább** gombra.

   ![Válassza ki a projekt varázsló képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. Az a **projekt konfigurálásához** képernyőn írja be **rövid** , **neve**, **samples.speech.cognitiveservices.microsoft.com** , **Csomagnév**, és válassza ki a projekt könyvtárában. A **minimális API-szintet** válasszon **API 23: Android 6.0 (marshmallow rendszert)** , hagyja az összes többi jelölőnégyzet nincs bejelölve, és válassza **Befejezés**.

   ![Képernyőfelvétel a projekt varázsló konfigurálása](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Az Android Studio előkészíti az új Android-projektet. Ezután úgy kell konfigurálnia a projektet, hogy az képes legyen használni a Speech SDK-t és a Java 8-at.

[!INCLUDE [License Notice](cognitive-services-speech-service-license-notice.md)]

A Cognitive Services Speech SDK jelenlegi verziója az `1.6.0`.

A beszédfelismerés SDK for Android van csomagolva, mint egy [AAR (Androidos függvénytár)](https://developer.android.com/studio/projects/android-library), amely tartalmazza a szükséges kódtárak és Android-engedélyek megadása kötelező.
A Maven tárházból, https vannak tárolva:\//csspeechstorage.blob.core.windows.net/maven/.

Készítse elő a projektet a Speech SDK használatára. A Project Structure (Projektstruktúra) ablak megnyitásához az Android Studio menüsávján válassza a **File (Fájl)**  > **Project Structure (Projektstruktúra)** elemet. A Project Structure (Projektstruktúra) ablakban hajtsa végre a következő módosításokat:

1. Az ablak bal oldalán található listából válassza ki a **Project** (Projekt) elemet. A **Default Library Repository** (Kódtár alapértelmezett adattára) értékéhez fűzzön hozzá egy vesszőt, majd a Maven-adattár aposztrófok közé zárt URL-címét. "https:\//csspeechstorage.blob.core.windows.net/maven/"

   ![A Project Structure (Projektstruktúra) ablak képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Ugyanezen képernyő bal oldalán válassza ki az **alkalmazást**. Ezután kattintson a **Dependencies** (Függőségek) fülre az ablak felső részén. Kattintson a zöld plusz jelre (+), majd a legördülő menüből válassza a **Library dependency** (Kódtárfüggőség) elemet.

   ![A Project Structure (Projektstruktúra) ablak képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. A megnyíló ablakban adja meg az androidos Speech SDK nevét és verzióját: `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0`. Ezután kattintson az **OK** gombra.
   A Speech SDK-nak ezek után meg kell jelennie a függőségek listáján, a következő módon:

   ![A Project Structure (Projektstruktúra) ablak képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Válassza a **Properties** (Tulajdonságok) lapot. A **Source Compatibility** (Forráskompatibilitás) és a **Target Compatibility** (Célkompatibilitás) beállításnál is az **1.8** értéket válassza.

   ![](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Az **OK** gombra kattintva zárja be a Project Structure (Projektstruktúra) ablakot, és alkalmazza a módosításokat a projektre.
