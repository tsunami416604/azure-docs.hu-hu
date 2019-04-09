---
title: 'Gyors útmutató: Beszédfelismerést, .NET-keretrendszer (Windows) – beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Ezt az útmutatót követve egy diktálási konzolalkalmazást hozhat létre a Windows-hoz készült .NET-keretrendszer és a Speech SDK használatával. Ha elkészült, a számítógép mikrofonjával valós időben konvertálhat át beszédet szöveggé.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: wolfma
ms.openlocfilehash: 2f190ccbead9e6349543d04e2419f458888fba2c
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59008747"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Gyors útmutató: Beszédfelismerést a Speech SDK-val .NET-keretrendszer (Windows)

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ezt az útmutatót követve egy diktálási konzolalkalmazást hozhat létre a Windows-hoz készült .NET-keretrendszer és a Speech SDK használatával. Ha elkészült, a számítógép mikrofonjával valós időben konvertálhat át beszédet szöveggé.

A gyors bemutató (nélkül létrehozása a Visual Studio-projekt magát az alább látható módon):

A legújabb verzió beszerzéséhez [Cognitive Services beszédfelismerő SDK-minták](https://github.com/Azure-Samples/cognitive-services-speech-sdk) a Githubról.

## <a name="prerequisites"></a>Előfeltételek

A projekt teljesítéséhez a következők szükségesek:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* A beszédfelismerési szolgáltatás egy előfizetési kulcsot. [Igényeljen ingyenesen egy](get-started.md).
* Hozzáférés a számítógép mikrofonjához

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a `Program.cs` fájlt, és cserélje le az automatikusan előállított kódot a következő mintára:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Keresse meg és cserélje le a karakterláncot `YourSubscriptionKey` az beszédszolgáltatások előfizetési kulccsal végzett.

1. Keresse meg és cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md). Ha például az ingyenes próbaverziót használja, akkor a régió a `westus`.

1. Mentse a projekt módosításait.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. A menüsávon válassza a **Létrehozás** > **Megoldás fordítása** elemet. A kód fordításának hiba nélkül végbe kell mennie.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Megoldás fordítása lehetőség](media/sdk/qs-csharp-dotnet-windows-08-build.png "Sikeres létrehozás")

1. Az alkalmazás elindításához a menüsávon válassza a **Hibakeresés** > **Hibakeresés indítása** elemet, vagy nyomja le az **F5** billentyűt.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Hibakeresés indítása lehetőség](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Hibakeresés indítása az alkalmazáson")

1. Ekkor megjelenik egy konzolablak, amely arra kéri Önt, hogy beszéljen. Mondjon valamit angolul. A beszéd továbbítani a beszédszolgáltatások, és a megjelenített érzéseket szöveg valós időben. Az eredmény a konzolon jelenik meg.

    ![Képernyőkép a konzolról a sikeres felismerést követően](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "A konzol a sikeres felismerést követően")

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a C# példák a Githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
