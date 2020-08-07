---
title: 'Gyors útmutató: beszéd-szöveg fordítása, C# (UWP) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.author: jhakulin
ms.topic: include
ms.openlocfilehash: 08da79afa14d883c6fe21ead7b3bca5f1aac5538
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/04/2020
ms.locfileid: "87771125"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [Állítsa be a fejlesztési környezetet, és hozzon létre egy üres projektet](../../../../quickstarts/setup-platform.md?tabs=uwp&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Mintakód hozzáadása

Most adja hozzá az alkalmazás felhasználói felületét meghatározó XAML-kódot, és adja hozzá a C#-kódot a megvalósítás mögött.

1. A **megoldáskezelő**megnyitásához nyissa meg a t `MainPage.xaml` .

1. A tervező XAML nézetében szúrja be a következő XAML-kódrészletet a **Grid** címkébe ( `<Grid>` és között `</Grid>` ):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml#StackPanel)]

1. A **megoldáskezelő**nyissa meg a kód mögötti forrásfájlt `MainPage.xaml.cs` . (Ez a következő alá van csoportosítva: `MainPage.xaml` .)

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml.cs#code)]

1. A `SpeechTranslationFromMicrophone_ButtonClicked` fájl kezelőjében keresse meg a karakterláncot `YourSubscriptionKey` , és cserélje le az előfizetési kulcsra.

1. A `SpeechTranslationFromMicrophone_ButtonClicked` kezelőben keresse meg a karakterláncot `YourServiceRegion` , és cserélje le az előfizetéséhez tartozó [régióra](~/articles/cognitive-services/Speech-Service/regions.md) . (Például használja `westus` az ingyenes próbaverziós előfizetést.)

1. A menüsávban válassza a **fájl**  >  **Mentés** lehetőséget a módosítások mentéséhez.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

Most már készen áll az alkalmazás létrehozására és tesztelésére.

1. Az alkalmazás létrehozásához a menüsávon válassza a **Build**  >  **Build megoldás** elemet. A kód fordításának hiba nélkül végbe kell mennie.

1. **Debug**  >  Az alkalmazás indításához válassza a hibakeresés**indítása hibakeresést** (vagy nyomja le az **F5**billentyűt). Megjelenik a **HelloWorld** ablak.

   ![Minta UWP fordítási alkalmazás C#-ban – gyors útmutató](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Válassza a **mikrofon engedélyezése**lehetőséget, és ha a hozzáférési engedély kérése megjelenik, válassza az **Igen**lehetőséget.

   ![Mikrofon-hozzáférési engedély kérése](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. **A mikrofon bemenetén válassza a beszéd fordítása**lehetőséget, és beszéljen egy angol kifejezéssel vagy mondattal az eszköz mikrofonjában. Az alkalmazás továbbítja a beszédet a Speech Service-nek, amely egy másik nyelven (ebben az esetben a német nyelven) szövegre fordítja a beszédet. A beszédfelismerési szolgáltatás visszaküldi a lefordított szöveget az alkalmazásnak, amely megjeleníti a fordítást az ablakban.

   ![Beszéd fordítás felhasználói felülete](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [footer](./footer.md)]
