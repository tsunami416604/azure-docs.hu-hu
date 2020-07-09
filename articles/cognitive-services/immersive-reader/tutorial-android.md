---
title: 'Oktatóanyag: a lebilincselő olvasó elindítása az Android-kód mintáinak használatával'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy példaként szolgáló Android-alkalmazást fog konfigurálni és futtatni, amely elindítja a magával ragadó olvasót.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: d847b4ab9f3c06634390e1f67dfed36df938c3ad
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050162"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-android-java-code-sample"></a>Oktatóanyag: a lebilincselő olvasó elindítása az Android Java code minta használatával

Az [Áttekintés](./overview.md)során megtanulta, hogy a magával ragadó olvasó Hogyan valósítja meg a bevált technikákat a nyelvtanulás, a feltörekvő olvasók és a tanulók tanulási különbségekkel való megismerésének javításához. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy olyan Android-alkalmazást, amely elindítja az olvasót. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Android-alkalmazás konfigurálása és futtatása egy minta-projekt használatával
> * Hozzáférési jogkivonat beszerzése
> * A részletes olvasó a minta tartalmának elindítása

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

* A Azure Active Directory hitelesítéshez konfigurált, magával ragadó olvasó erőforrás. A beállításhoz kövesse az [alábbi utasításokat](./how-to-create-immersive-reader.md) . A környezeti tulajdonságok konfigurálásakor itt létrehozott értékek némelyikére szüksége lesz. Mentse a munkamenet kimenetét szövegfájlba későbbi használatra.
* [Git](https://git-scm.com/)
* [Magával ragadó olvasói SDK](https://github.com/microsoft/immersive-reader-sdk)
* [Android Studio](https://developer.android.com/studio)

## <a name="configure-authentication-credentials"></a>Hitelesítő adatok konfigurálása

1. Indítsa el Android Studiot, és nyissa meg a projektet a- **olvasó-SDK/js/Samples/Gyorsindítás-Java-Android** címtárból (Java) vagy a- **olvasó-SDK/js/Samples/rövid útmutató-Kotlin** könyvtárából (Kotlin).

2. Hozzon létre egy **env** nevű fájlt a **/assets** mappához, és adja hozzá a következőt, a megfelelő módon adja meg az értékeket. Ügyeljen arra, hogy ne véglegesítse ezt a fájlt a verziókövetés során, mert olyan titkos kulcsokat tartalmaz, amelyeket nem kell nyilvánosságra hozni.

```text
TENANT_ID=<YOUR_TENANT_ID>
CLIENT_ID=<YOUR_CLIENT_ID>
CLIENT_SECRET=<YOUR_CLIENT_SECRET>
SUBDOMAIN=<YOUR_SUBDOMAIN>
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>A részletes olvasó a minta tartalmának elindítása

1. Válasszon egy eszköz-emulátort a AVD-kezelőből, és futtassa a projektet.

## <a name="next-steps"></a>További lépések

* Ismerkedjen meg a [magára az olvasói SDK](https://github.com/microsoft/immersive-reader-sdk) -val és az [olvasói SDK-referenciával](./reference.md)
* Kód mintáinak megtekintése a [githubon](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)