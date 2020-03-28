---
title: 'Rövid útmutató: Beszédfelolvasás, C# (UWP) – Beszédszolgáltatás fordítása'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 12/09/2019
ms.author: erhopf
ms.topic: include
ms.openlocfilehash: a0f62db319d54c2db71a86f621985a304dbbb4d2
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925826"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, győződjön meg arról, hogy:

> [!div class="checklist"]
> * [Azure-beszédfelismerési erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md?tabs=uwp)

## <a name="add-sample-code"></a>Mintakód hozzáadása

Most adja hozzá az XAML-kódot, amely meghatározza az alkalmazás felhasználói felületét, és adja hozzá a C# kód-mögötti implementációt.

1. A **Megoldáskezelőben**nyissa meg a programot. `MainPage.xaml`

1. A tervező XAML nézetében szúrja be a következő XAML-kódrészletet `</Grid>`a **Rácscímkébe** (a kettő között `<Grid>` és):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml#StackPanel)]

1. A **Megoldáskezelőben**nyissa meg a `MainPage.xaml.cs`mögötti kód forrásfájlt. (Ez alatt csoportosítva `MainPage.xaml`.)

1. Cserélje le az összes benne lévő kódot a következő kódrészletre:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml.cs#code)]

1. A `SpeechTranslationFromMicrophone_ButtonClicked` fájlkezelőben keresse meg `YourSubscriptionKey`a karakterláncot, és cserélje le az előfizetési kulcsra.

1. A `SpeechTranslationFromMicrophone_ButtonClicked` kezelőben keresse `YourServiceRegion`meg a karakterláncot , és cserélje le az előfizetéshez társított [régióra.](~/articles/cognitive-services/Speech-Service/regions.md) (Például használja `westus` az ingyenes próba-előfizetéshez.)

1. A módosítások mentéséhez válassza az > **Összes** **fájlmentése**lehetőséget a menüsorban.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

Most már készen áll az alkalmazás megépítésére és tesztelésére.

1. A menüsorban válassza **buildmegoldás összeállítása** > **az** alkalmazás létrehozásához. A kód fordításának hiba nélkül végbe kell mennie.

1. Az alkalmazás elindításához válassza a **Debug** > **Start Debugging** (vagy **az F5)** billentyűt. Megjelenik **a helloworld** ablak.

   ![Minta UWP fordítási alkalmazás C# nyelven - rövid útmutató](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Válassza **a Mikrofon engedélyezése**lehetőséget, és amikor megjelenik a hozzáférési engedélykérelem, válassza az **Igen**lehetőséget.

   ![Mikrofonhozzáférési engedély kérése](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Válassza **a Beszéd fordítása a mikrofonbemenetről**lehetőséget, és beszéljen egy angol kifejezést vagy mondatot az eszköz mikrofonjába. Az alkalmazás továbbítja a beszédet a beszéd szolgáltatás, amely lefordítja a beszéd szöveget egy másik nyelven (ebben az esetben, német). A beszédszolgáltatás elküldi a lefordított szöveget az alkalmazásnak, amely megjeleníti a fordítást az ablakban.

   ![Beszédfordítás felhasználói felülete](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]
