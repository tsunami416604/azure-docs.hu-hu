---
title: 'Oktatóanyag: a gyors iOS-mintakód használatával elindíthatja a lebilincselő olvasót'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy olyan minta Swift-alkalmazást fog konfigurálni és futtatni, amely elindítja az olvasót.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: a9259026747102dd65be3bb8da853735098667db
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050171"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-swift-ios-code-sample"></a>Oktatóanyag: a gyors iOS-mintakód használatával elindíthatja a lebilincselő olvasót

Az [Áttekintés](./overview.md)során megtanulta, hogy a magával ragadó olvasó Hogyan valósítja meg a bevált technikákat a nyelvtanulás, a feltörekvő olvasók és a tanulók tanulási különbségekkel való megismerésének javításához. Ez az oktatóanyag azt ismerteti, hogyan hozhat létre egy olyan iOS-alkalmazást, amely elindítja az olvasót. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az iOS-hez készült Swift-alkalmazás konfigurálása és futtatása minta-projekt használatával
> * Hozzáférési jogkivonat beszerzése
> * A részletes olvasó a minta tartalmának elindítása

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

* A Azure Active Directory hitelesítéshez konfigurált, magával ragadó olvasó erőforrás. A beállításhoz kövesse az [alábbi utasításokat](./how-to-create-immersive-reader.md) . A környezeti tulajdonságok konfigurálásakor itt létrehozott értékek némelyikére szüksége lesz. Mentse a munkamenet kimenetét szövegfájlba későbbi használatra.
* [macOS](https://www.apple.com/macos)
* [Git](https://git-scm.com/)
* [Magával ragadó olvasói SDK](https://github.com/microsoft/immersive-reader-sdk)
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)

## <a name="configure-authentication-credentials"></a>Hitelesítő adatok konfigurálása

1. Nyissa meg a Xcode, és nyissa meg a **immersive-Reader-SDK/js/Samples/iOS/Quickstart-Swift/Quickstart-Swift. xcodeproj**fájlt.
2. A felső menüben kattintson a **termék > séma > a séma szerkesztése..** . elemre.
3. A **Futtatás** nézetben kattintson az **argumentumok** fülre.
4. A **környezeti változók** szakaszban adja meg a következő neveket és értékeket, és adja meg a magával ragadó olvasó erőforrásának létrehozásakor megadott értékeket.

```text
TENANT_ID=<YOUR_TENANT_ID>
CLIENT_ID=<YOUR_CLIENT_ID>
CLIENT_SECRET<YOUR_CLIENT_SECRET>
SUBDOMAIN=<YOUR_SUBDOMAIN>
```

Ügyeljen arra, hogy ne véglegesítse a fenti módosításokat a verziókövetés során, mert olyan titkos kulcsokat tartalmaz, amelyeket nem kell nyilvánosságra hozni.

## <a name="launch-the-immersive-reader-with-sample-content"></a>A részletes olvasó a minta tartalmának elindítása

1. A Xcode nyomja le a **CTRL-R** billentyűkombinációt a projekt futtatásához.

## <a name="next-steps"></a>További lépések

* Ismerkedjen meg a [magára az olvasói SDK](https://github.com/microsoft/immersive-reader-sdk) -val és az [olvasói SDK-referenciával](./reference.md)
* Kód mintáinak megtekintése a [githubon](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
