---
title: 'Gyors útmutató: Beszéd fordítása C# , (UWP) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban egy Univerzális Windows-platform-(UWP-) alkalmazást fog létrehozni a felhasználói beszéd rögzítéséhez, lefordítani egy másik nyelvre, és kiírja a szöveget a parancssorba. Ez az útmutató Windows-felhasználók számára készült.
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 08/19/2019
ms.author: erhopf
ms.topic: quickstart
ms.openlocfilehash: e513cbbc615965ef196a830351aab8ac241c3f20
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382611"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>Gyors útmutató: Beszéd fordítása a Speech SDK for C# (UWP) használatával

A [beszédfelismerés](quickstart-csharp-uwp.md), a [beszédfelismerés](quickstart-text-to-speech-csharp-uwp.md)és a [hang-első virtuális asszisztens](quickstart-virtual-assistant-csharp-uwp.md)is elérhetővé teszi a gyors üzembe helyezést.

Ebben a rövid útmutatóban egy Univerzális Windows-platform (UWP) alkalmazást fog létrehozni, amely rögzíti a számítógép mikrofonjának felhasználói beszédét, lefordítja a beszédet, és valós időben átírja a lefordított szöveget a parancssorba. Ez az alkalmazás a 64 bites Windows rendszeren fut, és a [SPEECH SDK NuGet csomaggal](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2019-mel készült.

A beszédfelismeréshez elérhető nyelvek teljes listáját a [nyelvi támogatás](language-support.md)című témakörben tekintheti meg.

> [!NOTE]
> A UWP lehetővé teszi olyan alkalmazások fejlesztését, amelyek a Windows 10 rendszert támogató bármely eszközön futnak, beleértve a PC-ket, az Xboxot, a Surface Hubt és az egyéb eszközöket.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz. [Szerezze be az egyiket ingyenesen](get-started.md).

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

Most adja hozzá az alkalmazás felhasználói felületét meghatározó XAML-kódot, és adja hozzá a C# kód mögötti implementációt.

1. A **megoldáskezelő**megnyitásához `MainPage.xaml`nyissa meg a t.

1. A tervező XAML nézetében szúrja be a következő XAML-kódrészletet a **Grid** címkébe `<Grid>` ( `</Grid>`és között):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. A **megoldáskezelő**nyissa meg a kód mögötti forrásfájlt `MainPage.xaml.cs`. (Ez a következő alá `MainPage.xaml`van csoportosítva:.)

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. A fájl `YourSubscriptionKey`kezelőjében keresse meg a karakterláncot, és cserélje le az előfizetési kulcsra. `SpeechTranslationFromMicrophone_ButtonClicked`

1. A kezelőben keresse meg a karakterláncot `YourServiceRegion`, és cserélje le az előfizetéséhez tartozó [régióra](regions.md) . `SpeechTranslationFromMicrophone_ButtonClicked` (Például használja `westus` az ingyenes próbaverziós előfizetést.)

1. A menüsávban válassza a **fájl** > **Mentés** lehetőséget a módosítások mentéséhez.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

Most már készen áll az alkalmazás létrehozására és tesztelésére.

1. Az alkalmazás létrehozásához a > menüsávon **válassza a Build** **Build megoldás** elemet. A kód fordításának hiba nélkül végbe kell mennie.

1. Az alkalmazás **indításához válassza a hibakeresés** **indítása hibakeresést** (vagy nyomja le az F5 billentyűt). >  Megjelenik a **HelloWorld** ablak.

   ![Példa UWP C# -fordítói alkalmazásra – gyors üzembe helyezés](media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Válassza a **mikrofon engedélyezése**lehetőséget, és ha a hozzáférési engedély kérése megjelenik, válassza az **Igen**lehetőséget.

   ![Mikrofon-hozzáférési engedély kérése](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. **A mikrofon bemenetén válassza a beszéd fordítása**lehetőséget, és beszéljen egy angol kifejezéssel vagy mondattal az eszköz mikrofonjában. Az alkalmazás továbbítja a beszédet a Speech Service-nek, amely egy másik nyelven (ebben az esetben a német nyelven) szövegre fordítja a beszédet. A beszédfelismerési szolgáltatás visszaküldi a lefordított szöveget az alkalmazásnak, amely megjeleníti a fordítást az ablakban.

   ![Beszéd fordítás felhasználói felülete](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Minták C# feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Custom Speech modell betanítása](how-to-custom-speech-train-model.md)
