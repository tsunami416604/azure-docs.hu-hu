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
ms.openlocfilehash: 8418e2d7a4b3800742bc4ecc777222ebca07aee2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076888"
---
# <a name="tutorial-start-the-immersive-reader-using-the-android-java-code-sample"></a>Oktatóanyag: a lebilincselő olvasó elindítása az Android Java code minta használatával

Az [Áttekintés](./overview.md)során megtanulta, hogy a magával ragadó olvasó Hogyan valósítja meg a bevált technikákat a nyelvtanulás, a feltörekvő olvasók és a tanulók tanulási különbségekkel való megismerésének javításához. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Android-alkalmazást, amely elindítja a magával ragadó olvasót. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Android-alkalmazások konfigurálása és futtatása egy minta projekt használatával.
> * Hozzáférési jogkivonat beszerzése.
> * A részletes olvasót a minta tartalmával indíthatja el.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

* A Azure Active Directory hitelesítéshez konfigurált, magával ragadó olvasó erőforrás. A beállításhoz kövesse az [alábbi utasításokat](./how-to-create-immersive-reader.md) . A környezeti tulajdonságok konfigurálásakor itt létrehozott értékeket kell megadnia. Mentse a munkamenet kimenetét szövegfájlba későbbi használatra.
* [Git](https://git-scm.com/).
* [Magával ragadó olvasói SDK](https://github.com/microsoft/immersive-reader-sdk).
* [Android Studio](https://developer.android.com/studio).

## <a name="configure-authentication-credentials"></a>Hitelesítő adatok konfigurálása

1. Indítsa el a Android Studiot, és nyissa meg a projektet a- **olvasó-SDK/js/Samples/Gyorsindítás-Java-Android** címtárból (Java) vagy a- **olvasó-SDK/js/Samples/Gyorsindítás-Kotlin** könyvtárából (Kotlin).

1. Hozzon létre egy **env** nevű fájlt a **/assets** mappában. Adja hozzá a következő neveket és értékeket, és adja meg a megfelelő értékeket. Ne véglegesítse ezt a fájlt a verziókövetés során, mert olyan titkos kulcsokat tartalmaz, amelyeket nem szabad nyilvánosságra hozni.
    
    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET=<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

## <a name="start-the-immersive-reader-with-sample-content"></a>A részletes olvasó a minta tartalmának elindítása

Válasszon egy eszköz-emulátort a AVD-kezelőből, és futtassa a projektet.

## <a name="next-steps"></a>További lépések

* Fedezze fel az [olvasói SDK](https://github.com/microsoft/immersive-reader-sdk) -t és a [magára ejtő olvasó SDK-referenciát](./reference.md).
* Kód mintáinak megtekintése a [githubon](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).