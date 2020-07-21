---
title: 'Oktatóanyag: a gyors iOS-mintakód használatával megkezdheti a gördülékeny olvasót'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy olyan minta Swift-alkalmazást fog konfigurálni és futtatni, amely elindítja a lebilincselő olvasót.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: d9bb3a5fb41215f2e839efc7989ea7854f254f16
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537935"
---
# <a name="tutorial-start-the-immersive-reader-using-the-swift-ios-code-sample"></a>Oktatóanyag: a gyors iOS-mintakód használatával megkezdheti a gördülékeny olvasót

Az [Áttekintés](./overview.md)során megtanulta, hogy a magával ragadó olvasó Hogyan valósítja meg a bevált technikákat a nyelvtanulás, a feltörekvő olvasók és a tanulók tanulási különbségekkel való megismerésének javításához. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy olyan iOS-alkalmazást, amely elindítja a magával ragadó olvasót. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az iOS-hez készült Swift-alkalmazás konfigurálása és futtatása egy minta projekt használatával.
> * Hozzáférési jogkivonat beszerzése.
> * A részletes olvasót a minta tartalmával indíthatja el.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

* A Azure Active Directory hitelesítéshez konfigurált, magával ragadó olvasó erőforrás. A beállításhoz kövesse az [alábbi utasításokat](./how-to-create-immersive-reader.md) . A környezeti tulajdonságok konfigurálásakor itt létrehozott értékeket kell megadnia. Mentse a munkamenet kimenetét szövegfájlba későbbi használatra.
* [MacOS](https://www.apple.com/macos).
* [Git](https://git-scm.com/).
* [Magával ragadó olvasói SDK](https://github.com/microsoft/immersive-reader-sdk).
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="configure-authentication-credentials"></a>Hitelesítő adatok konfigurálása

1. Nyissa meg a Xcode, és nyissa meg a **immersive-Reader-SDK/js/Samples/iOS/Quickstart-Swift/Quickstart-Swift. xcodeproj**fájlt.
1. A felső menüben válassza a **termék**  >  **sémájának**  >  **szerkesztési sémája**lehetőséget.
1. A **Futtatás** nézetben válassza az **argumentumok** fület.
1. A **környezeti változók** szakaszban adja hozzá a következő neveket és értékeket. Adja meg azokat az értékeket, amelyeket a magától elolvasó erőforrás létrehozásakor adott meg.

    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

Ne véglegesítse ezt a változást a verziókövetés során, mert olyan titkos kulcsokat tartalmaz, amelyeket nem szabad nyilvánosságra hozni.

## <a name="start-the-immersive-reader-with-sample-content"></a>A részletes olvasó a minta tartalmának elindítása

A Xcode válassza a **CTRL + R** billentyűkombinációt a projekt futtatásához.

## <a name="next-steps"></a>Következő lépések

* Fedezze fel az [olvasói SDK](https://github.com/microsoft/immersive-reader-sdk) -t és a [magára ejtő olvasó SDK-referenciát](./reference.md).
* Kód mintáinak megtekintése a [githubon](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
