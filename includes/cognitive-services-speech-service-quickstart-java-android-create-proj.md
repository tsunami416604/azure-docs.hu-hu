---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 5/23/2019
ms.author: travisw
ms.openlocfilehash: e1f9b561efb1fd88fe2dd00f8c175f1ebae848d8
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391657"
---
1. Indítsa el az Android Studio alkalmazást, majd az üdvözlőablakban válassza a **Start a new Android Studio project** (Új Android Studio-projekt indítása) elemet.

    ![Az Android Studio üdvözlőablakának képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Megjelenik a **projekt kiválasztása** varázsló, és válassza a **telefon és tábla** és az **üres tevékenységek** lehetőséget a tevékenység kiválasztása mezőben. Kattintson a **Tovább** gombra.

   ![A projekt kiválasztása varázsló képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. A **projekt konfigurálása** **képernyőn írja be** a gyors útmutató **nevet**, **Samples.Speech.cognitiveservices.microsoft.com** nevet, **és**válassza ki a projekt könyvtárát. A **minimális API-szint** pick **API 23: Android 6,0 (Marshmallow)** esetében ne jelölje be az összes többi jelölőnégyzetet, majd kattintson a **Befejezés gombra**.

   ![A projekt konfigurálása varázsló képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Az Android Studio előkészíti az új Android-projektet. Ezután úgy kell konfigurálnia a projektet, hogy az képes legyen használni a Speech SDK-t és a Java 8-at.

[!INCLUDE [License Notice](cognitive-services-speech-service-license-notice.md)]

A Cognitive Services Speech SDK jelenlegi verziója az `1.7.0`.

Az Androidhoz készült Speech SDK a szükséges kódtárakat és a szükséges [Android-engedélyeket](https://developer.android.com/studio/projects/android-library)tartalmazza.
A szolgáltatás egy Maven-tárházban üzemel, https: \//csspeechstorage. blob. Core. Windows. net/Maven/.

Készítse elő a projektet a Speech SDK használatára. A Project Structure (Projektstruktúra) ablak megnyitásához az Android Studio menüsávján válassza a **File (Fájl)**  > **Project Structure (Projektstruktúra)** elemet. A Project Structure (Projektstruktúra) ablakban hajtsa végre a következő módosításokat:

1. Az ablak bal oldalán található listából válassza ki a **Project** (Projekt) elemet. A **Default Library Repository** (Kódtár alapértelmezett adattára) értékéhez fűzzön hozzá egy vesszőt, majd a Maven-adattár aposztrófok közé zárt URL-címét. ' https: \//csspeechstorage. blob. Core. Windows. net/Maven/'

   ![A Project Structure (Projektstruktúra) ablak képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Ugyanezen képernyő bal oldalán válassza ki az **alkalmazást**. Ezután kattintson a **Dependencies** (Függőségek) fülre az ablak felső részén. Kattintson a zöld plusz jelre (+), majd a legördülő menüből válassza a **Library dependency** (Kódtárfüggőség) elemet.

   ![A Project Structure (Projektstruktúra) ablak képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. A megnyíló ablakban adja meg az androidos Speech SDK nevét és verzióját: `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`. Ezután kattintson az **OK** gombra.
   A Speech SDK-nak ezek után meg kell jelennie a függőségek listáján, a következő módon:

   ![A Project Structure (Projektstruktúra) ablak képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Válassza a **Tulajdonságok** lapot. A **forrás-kompatibilitás** és a **cél kompatibilitás**érdekében válassza a **1,8**lehetőséget.

   ![](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Az **OK** gombra kattintva zárja be a Project Structure (Projektstruktúra) ablakot, és alkalmazza a módosításokat a projektre.
