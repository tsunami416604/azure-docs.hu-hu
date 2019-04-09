---
title: 'Gyors útmutató: Szintetizálásához beszéd átalakítás, .NET-keretrendszer (Windows) – beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Ez az útmutató segítségével hozzon létre egy szöveg-hang transzformációs konzolalkalmazást a Windows és a Speech SDK a .NET-keretrendszer használatával. Amikor végzett, beszéd szövegből szintetizálásához, és hallgassa meg a beszéd valós időben a beszélő a.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 4/03/2019
ms.author: yinhew
ms.openlocfilehash: a013189e45b1c1c8eeb88d62a718d495c0c415a2
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012247"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Gyors útmutató: Beszédfelismerés, beszédfelismerési SDK-val szintetizálásához Pro rozhraní .NET Framework (Windows)

Ez az útmutató segítségével hozzon létre egy szöveg-hang transzformációs konzolalkalmazást a Windows és a Speech SDK a .NET-keretrendszer használatával. Amikor végzett, beszéd szövegből szintetizálásához, és hallgassa meg a beszéd valós időben a beszélő a.

A gyors bemutató (nélkül létrehozása a Visual Studio-projekt magát az alább látható módon):

A legújabb verzió beszerzéséhez [Cognitive Services beszédfelismerő SDK-minták](https://github.com/Azure-Samples/cognitive-services-speech-sdk) a Githubról.

## <a name="prerequisites"></a>Előfeltételek

A projekt teljesítéséhez a következők szükségesek:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* A beszédfelismerési szolgáltatás egy előfizetési kulcsot. [Igényeljen ingyenesen egy](get-started.md).
* A speaker (vagy mikrofonos) érhető el.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a `Program.cs` fájlt, és cserélje le az automatikusan előállított kódot a következő mintára:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Keresse meg és cserélje le a karakterláncot `YourSubscriptionKey` az beszédszolgáltatások előfizetési kulccsal végzett.

1. Keresse meg és cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md). Ha például az ingyenes próbaverziót használja, akkor a régió a `westus`.

1. Mentse a projekt módosításait.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. A menüsávon válassza a **Létrehozás** > **Megoldás fordítása** elemet. A kód fordításának hiba nélkül végbe kell mennie.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Megoldás fordítása lehetőség](media/sdk/qs-csharp-dotnet-windows-08-build.png "Sikeres létrehozás")

1. Az alkalmazás elindításához a menüsávon válassza a **Hibakeresés** > **Hibakeresés indítása** elemet, vagy nyomja le az **F5** billentyűt.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Hibakeresés indítása lehetőség](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Hibakeresés indítása az alkalmazáson")

1. A konzolablakban megjelenik, írjon be valamilyen szöveget, és felszólítja. Írjon be néhány szavakat és a egy mondatot. A beírt szöveget a beszédszolgáltatások továbbítani, és a Speech, amely a a hangfelismerő játszik synthesized.

    ![Képernyőkép a konzolról a sikeres felismerést követően](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "A konzol a sikeres felismerést követően")

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a C# példák a Githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Hangtípusok testreszabása](how-to-customize-voice-font.md)
- [Hangminták felvétele](record-custom-voice-samples.md)
