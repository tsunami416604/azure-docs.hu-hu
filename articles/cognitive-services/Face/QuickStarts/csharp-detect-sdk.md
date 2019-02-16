---
title: 'Gyors útmutató: A Face Azure .NET SDK-val képet arcok észlelése'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az Azure Face rendelkező SDK-t fogja használni C# arcokat észleli a képet.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 02/06/2019
ms.author: pafarley
ms.openlocfilehash: eaaeda1460ecffc2b1a53bb46901aedc2e9563c4
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313313"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-net-sdk"></a>Gyors útmutató: Arcfelismerés a képet, a Face .NET SDK használatával

Az ebben a rövid útmutatóban a Face fogja használni az SDK szolgáltatás C# emberi arcok észlelése a képet. Ebben a rövid útmutatóban a kód egy működő példa: az Arcfelismerés projekt a a [Cognitive Services Látástechnológia csharp útmutatóink](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face) adattárat a Githubon.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek

- A Face API előfizetési kulcs. Megjelenik a származó ingyenes próba-előfizetését kulcsok [próbálja meg a Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Másik lehetőségként kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Face API szolgáltatás és a kulcs beszerzése.
- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.

## <a name="create-the-visual-studio-project"></a>A Visual Studio-projekt létrehozása

1. A Visual Studióban hozzon létre egy új **Console app (.NET Framework)** projektre, és adja neki **FaceDetection**. 
1. Ha más projektek is vannak a megoldásban, válassza ki ezt a projektet az egyedüli kezdőprojektként.
1. Szerezze be a szükséges NuGet-csomagokat. Kattintson a jobb gombbal a projektre a Megoldáskezelőben, és válassza ki **NuGet-csomagok kezelése**. Kattintson a **Tallózás** lapot, és válasszon **előzetes verzió**; majd keresse meg és telepítse a következő csomagot:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="add-face-detection-code"></a>Arcok észlelése kód hozzáadása

Nyissa meg az új projekt *Program.cs* fájlt. Itt fogja hozzáadni a képeket tölthet be és arcfelismerés szükséges kódot.

### <a name="include-namespaces"></a>Névterek belefoglalása

Adja hozzá az alábbi `using` utasításokat a *Program.cs* fájl elejéhez.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=1-7)]

### <a name="add-essential-fields"></a>Alapvető mezők hozzáadása

Adja hozzá a **Program** osztályhoz a következő mezőket: Ezeket az adatokat adja meg, hogyan csatlakozhat a Face szolgáltatás és a bemeneti adatok beszerzése. Frissíteni kell a `subscriptionKey` mező értékét az előfizetési kulcs, és előfordulhat, hogy módosítania kell a `faceEndpoint` úgy, hogy a megfelelő régióazonosító tartalmaz. Is kell beállítani a `localImagePath` és/vagy `remoteImageUrl` elérési utakhoz, mutasson a tényleges értékek képfájlok.

A `faceAttributes` mező értéke csak bizonyos típusú attribútumok tömbje. Azt határozza meg az észlelt arcok kapcsolatos lekérni kívánt adatokat.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=13-34)]

### <a name="create-and-use-the-face-client"></a>Hozzon létre, és a Face ügyfél használata

Ezután adja hozzá a következő kódot a **fő** módszere a **Program** osztály. A Face API-ügyfél állít be.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=38-41)]

A a **fő** metódust, adja hozzá a következő kódot arcfelismerés helyi és távoli képen az újonnan létrehozott Face ügyfél használatával. Észlelési módszerek meghatározva mellett. 

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=43-49)]

### <a name="detect-faces"></a>Arcfelismerés

Adja hozzá a **Program** osztályhoz a következő metódust. A Face szolgáltatásügyfél arcfelismerés hivatkozik egy URL-címet, egy távoli képen használ. Vegye figyelembe, hogy használja a `faceAttributes` mező&mdash;a **DetectedFace** adott objektumok `faceList` fog rendelkezni a megadott attribútumok (Ez esetben, életkor és nem).

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=52-74)]

Hasonlóképpen, adja hozzá a **DetectLocalAsync** metódust. A Face szolgáltatásügyfél arcfelismerés a egy helyi, a fájl elérési útja által hivatkozott rendszerképet használ.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=76-101)]

### <a name="retrieve-and-display-face-attributes"></a>Lekérni, és olyan tulajdonságok megjelenítése

Ezt követően adja meg a **GetFaceAttributes** metódust. Ez a kapcsolódó attribútum információkkal karakterláncot ad vissza.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=103-116)]

Végül adja meg a **DisplayAttributes** metódus face attribútum adatokat írni a konzol kimenete.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=118-123)]

## <a name="run-the-app"></a>Az alkalmazás futtatása

A sikeres válasz megjelenik a nemek és a korszűrő minden a képen. Példa:

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy egyszerű .NET konzolalkalmazást, amely a Face API szolgáltatás segítségével a helyi és távoli képeken arcok észlelése. Ezután az oktatóanyag egy részletesebb megtekintéséhez, hogyan, képesek adatokat megjeleníteni az arcok a felhasználó egy intuitív módon.

> [!div class="nextstepaction"]
> [Oktatóanyag: Észlelheti és elemezheti az arcokat a képet a WPF-alkalmazás létrehozása](../Tutorials/FaceAPIinCSharpTutorial.md)
