---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: travisw
ms.openlocfilehash: 8b187e058299f8aa8b762231c0ed1e708e5ad9d1
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80659053"
---
1. Indítsa el az Android Studio alkalmazást, és válassza az **Új Android Studio-projekt indítása lehetőséget** az **Üdvözlőablakban.**

    ![Az Android Studio üdvözlőablakának képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Megjelenik **a Projekt kiválasztása** varázsló. A tevékenységkijelölési mezőben válassza a **Telefon és táblagép** és a Tevékenység **kiürítése** jelölőnégyzetet. Válassza a **Tovább lehetőséget.**

   ![Képernyőkép: A projekt kiválasztása varázsló](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. A **Projekt konfigurálása** képernyőn írja be a *Rövid útmutató* **nevet,** és írja be *samples.speech.cognitiveservices.microsoft.com* **csomagnévként.** Ezután jelöljön ki egy projektkönyvtárat. A **Minimális API szint**hez válassza az API **23: Android 6.0 (Marshmallow)** lehetőséget. Hagyja az összes többi jelölőnégyzetet, és válassza a **Befejezés gombot.**

   ![Képernyőkép: A projekt konfigurálása varázsló](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Az Android Studio előkészíti az új Android-projektet. Ezután konfigurálja a projektet az Azure Cognitive Services Speech SDK-ról való tudnivalókszerint, és használja a Java 8-at.

[!INCLUDE [License notice](cognitive-services-speech-service-license-notice.md)]

A Cognitive Services beszédstka jelenlegi verziója 1.11.0.

Az Android-alapú beszédsdkát [AAR (Android Library)](https://developer.android.com/studio/projects/android-library)néven csomagolják, amely tartalmazza a szükséges könyvtárakat és a szükséges Android-engedélyeket.
A maven-tárházban található https:\//csspeechstorage.blob.core.windows.net/maven/.

Készítse elő a projektet a Speech SDK használatára. Nyissa meg a **Projektstruktúra** ablakot az Android Studio menüsor > **Fájlprojekt-struktúra parancsára** kattintva. **File** A **Projektszerkezet ablakban** hajtsa végre a következő módosításokat:

1. Az ablak bal oldalán található listából válassza ki a **Project** (Projekt) elemet. Az **alapértelmezett tártárház** beállításainak szerkesztése egy vessző és a Maven-tárház URL-címének\/egyszeres idézőjelek közé ágyazásával: 'https: /csspeechstorage.blob.core.windows.net/maven/'

   ![A Project Structure (Projektstruktúra) ablak képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Ugyanazon a képernyőn, a bal oldalon válassza az **alkalmazást**. Ezután kattintson a **Dependencies** (Függőségek) fülre az ablak felső részén. Jelölje ki a**+** zöld pluszjelet ( ), majd a legördülő menü **könyvtári függőségparancsát.**

   ![Képernyőkép a Könyvtár-függőségről](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. A megjelenő ablakban adja meg az Android-alapú Beszéd sdk nevét és verzióját, *com.microsoft.cognitiveservices.speech:client-sdk:1.11.0*. Ezután kattintson az **OK** gombra.
   A beszédsdk-et most kell hozzáadni a függőségek listájához, ahogy az látható:

   ![Képernyőkép a beszédfelismerési SDK-ról a függőségek listájában](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Válassza a **Tulajdonságok** lapot. A **forráskompatibilitás** és **a célkompatibilitás érdekében**válassza az **1.9**lehetőséget.

   ![Képernyőkép a forráskompatibilitásról és a célkompatibilitásról](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Az **OK gombra választva** zárja be a **Projektszerkezet ablakot,** és alkalmazza a módosításokat a projektre.
