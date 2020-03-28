---
title: 'Rövid útmutató: Beszéd szintetizálása, C# (UWP) – beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben létre fog hozni egy C# Universal Windows Platform- (UWP-) alkalmazást a Cognitive Services Speech SDK használatával. A szövegből valós időben szintetizálhatja a beszédet az eszköz hangszórójáig. Az alkalmazás a Speech SDK NuGet csomaggal és a Microsoft Visual Studio 2019-tel készült.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 8ca97be2863bd8e45ac7937c49c464fa2f216b11
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925327"
---
> [!NOTE]
> A Universal Windows Platformon olyan alkalmazásokat fejleszthet, amelyek a Windows 10-et támogató minden eszközön futtathatók: PC-n, Xboxon, Surface Hubon stb.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, győződjön meg arról, hogy:

> [!div class="checklist"]
> * [Azure-beszédfelismerési erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md?tabs=uwp)

## <a name="add-sample-code"></a>Mintakód hozzáadása

Most adja hozzá az XAML-kódot, amely meghatározza az alkalmazás felhasználói felületét, és adja hozzá a C# kód-mögötti implementációt.

1. A **Megoldáskezelőben**nyissa meg a programot. `MainPage.xaml`

1. A tervező XAML nézetében szúrja be a következő XAML-kódrészletet `</Grid>`a **Rácscímkébe** (a kettő között `<Grid>` és):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. A **Megoldáskezelőben**nyissa meg a `MainPage.xaml.cs`mögötti kód forrásfájlt. (Ez alatt csoportosítva `MainPage.xaml`.)

1. Cserélje le az összes benne lévő kódot a következő kódrészletre:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. A forrásfájl kezelőjében `Speak_ButtonClicked` keresse `YourSubscriptionKey`meg a karakterláncot , és cserélje le az előfizetési kulcsra.

1. A `Speak_ButtonClicked` kezelőben keresse `YourServiceRegion`meg a karakterláncot , és cserélje le az előfizetéshez társított [régióra.](~/articles/cognitive-services/Speech-Service/regions.md) (Például használja `westus` az ingyenes próba-előfizetéshez.)

1. A módosítások mentéséhez válassza az > **Összes** **fájlmentése**lehetőséget a menüsorban.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

Most már készen áll az alkalmazás megépítésére és tesztelésére.

1. A menüsorban válassza a Build megoldás **összeállítása** > **Build Solution** az alkalmazás létrehozásához. A kód fordításának hiba nélkül végbe kell mennie.

1. Az alkalmazás elindításához válassza a **Debug** > **Start Debugging** (vagy **az F5)** billentyűt. Megjelenik **a helloworld** ablak.

   ![Minta UWP beszédszintetizáló alkalmazás C# nyelven – rövid útmutató](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Írjon be néhány szöveget a szövegmezőbe, és kattintson a **Felolvasás gombra.** A szöveg továbbítása a beszédfelismerési szolgáltatásba kerül, és beszédre szintetizálódik, amely a hangszórón szól.

    ![Beszédszintézis felhasználói felülete](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Lásd még

- [Egyéni hang létrehozása](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
