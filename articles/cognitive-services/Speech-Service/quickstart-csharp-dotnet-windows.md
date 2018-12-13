---
title: 'Gyors útmutató: Recognize speech, .NET-keretrendszer (Windows) – beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Ezt az útmutatót követve egy diktálási konzolalkalmazást hozhat létre a Windows-hoz készült .NET-keretrendszer és a Speech SDK használatával. Ha elkészült, a számítógép mikrofonjával valós időben konvertálhat át beszédet szöveggé.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/05/2018
ms.author: wolfma
ms.openlocfilehash: 171d6c6a972d6fa3eb2e96ceacb75dd2fce1eb92
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074895"
---
# <a name="quickstart-recognize-and-transcribe-speech-using-the-speech-sdk-and-net-framework-windows"></a>Rövid útmutató: Beszéd felismerése és szöveggé konvertálása a Speech SDK és a .NET Framework (Windows) használatával

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ezt az útmutatót követve egy diktálási konzolalkalmazást hozhat létre a Windows-hoz készült .NET-keretrendszer és a Speech SDK használatával. Ha elkészült, a számítógép mikrofonjával valós időben konvertálhat át beszédet szöveggé.

Ehhez a rövid útmutatóhoz szükség van egy [Azure Cognitive Services-fiókra](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amelyhez engedélyezve van a Microsoft Speech szolgáltatás. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

A projekt teljesítéséhez a következők szükségesek:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Előfizetői azonosító a Speech Service-hez
* Hozzáférés a számítógép mikrofonjához

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [Create project ](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a `Program.cs` fájlt, és cserélje le az automatikusan előállított kódot a következő mintára:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Keresse meg és cserélje le a `YourSubscriptionKey` sztringet a Speech Service előfizetői azonosítójára.

1. Keresse meg és cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md). Ha például az ingyenes próbaverziót használja, akkor a régió a `westus`.

1. Mentse a projekt módosításait.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. A menüsávon válassza a **Létrehozás** > **Megoldás fordítása** elemet. A kód fordításának hiba nélkül végbe kell mennie.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Megoldás fordítása lehetőség](media/sdk/qs-csharp-dotnet-windows-08-build.png "Sikeres létrehozás")

1. Az alkalmazás elindításához a menüsávon válassza a **Hibakeresés** > **Hibakeresés indítása** elemet, vagy nyomja le az **F5** billentyűt.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Hibakeresés indítása lehetőség](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Hibakeresés indítása az alkalmazáson")

1. Ekkor megjelenik egy konzolablak, amely arra kéri Önt, hogy beszéljen. Mondjon valamit angolul. A rendszer továbbítja a beszédet a Speech Service-be, és valós időben szöveggé alakítja át. Az eredmény a konzolon jelenik meg.

    ![Képernyőkép a konzolról a sikeres felismerést követően](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "A konzol a sikeres felismerést követően")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
A kód a `quickstart/csharp-dotnet-windows` mappában érhető el.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szándék felismerése beszédből a C#-hez készült Speech SDK használatával](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>Lásd még

- [Beszéd fordítása](how-to-translate-speech-csharp.md)
- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
