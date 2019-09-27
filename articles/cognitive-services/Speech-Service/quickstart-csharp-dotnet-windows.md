---
title: 'Gyors útmutató: Beszédfelismerés felismerése, .NET-keretrendszer (Windows) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Ezt az útmutatót követve egy diktálási konzolalkalmazást hozhat létre a Windows-hoz készült .NET-keretrendszer és a Speech SDK használatával. Ha elkészült, a számítógép mikrofonjával valós időben konvertálhat át beszédet szöveggé.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: wolfma
ms.openlocfilehash: cb140647394858fbc0a9a00ea125365d5b7a08d5
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327044"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Gyors útmutató: Beszéd felismerése a .NET-keretrendszerhez készült Speech SDK-val (Windows)

A [beszédfelismerés](quickstart-text-to-speech-dotnet-windows.md) és a [beszéd fordítása](quickstart-translate-speech-dotnetframework-windows.md)is elérhető.

Ha szeretné, válasszon másik programozási nyelvet és/vagy környezetet:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ezt az útmutatót követve egy diktálási konzolalkalmazást hozhat létre a Windows-hoz készült .NET-keretrendszer és a Speech SDK használatával. Ha elkészült, a számítógép mikrofonjával valós időben konvertálhat át beszédet szöveggé.

Egy gyors bemutatóhoz (a Visual Studio-projekt létrehozása nélkül, a jelen cikkben leírtak szerint) szerezze be a legfrissebb [Cognitive Services SPEECH SDK-mintákat](https://github.com/Azure-Samples/cognitive-services-speech-sdk) a githubról.

## <a name="prerequisites"></a>Előfeltételek

A projekt teljesítéséhez a következők szükségesek:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* A beszédfelismerési szolgáltatáshoz tartozó előfizetési kulcs. [Szerezze be az egyiket ingyenesen](get-started.md).
* A számítógép mikrofonjának elérése.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a **program.cs** , és cserélje le az automatikusan generált kódot a következő mintára:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Keresse meg a `YourSubscriptionKey` karakterláncot, és cserélje le a Speech Services előfizetési kulcsára.

1. Keresse meg a `YourServiceRegion` karakterláncot, és cserélje le az előfizetéshez társított [régióra](regions.md) . Ha például az ingyenes próbaverziós előfizetést használja, akkor a régió `westus`.

1. A menüsávban válassza a **fájl**@no__t – 1**Mentés összes mentése**elemet.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. A menüsávban válassza a **build** > **Build megoldás** elemet az alkalmazás létrehozásához. A kód fordításának hiba nélkül végbe kell mennie.

1. A **HelloWorld** alkalmazás indításához válassza a **hibakeresés @no__t –** 1**Indítás hibakeresése** lehetőséget (vagy válassza az **F5 billentyűt**).

1. Beszéljen egy angol kifejezéssel vagy mondattal az eszköz mikrofonjában. Az alkalmazás továbbítja a beszédet a beszédfelismerési szolgáltatásoknak, amelyek az átvitt szöveget visszaküldik az alkalmazásnak a megjelenítéshez.

   ![Beszédfelismerés felhasználói felülete](media/sdk/qs-csharp-dotnet-windows-10-console-output.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Minták C# feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Custom Speech modell betanítása](how-to-custom-speech-train-model.md)
