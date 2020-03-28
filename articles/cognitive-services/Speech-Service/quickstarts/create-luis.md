---
title: 'Rövid útmutató: LUIS-kulcs létrehozása'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre egy LUIS-alkalmazást, és hogyan szerezhet be egy kulcsot.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 5b1a5ac4867379457d161d07f4f4f2fc2d8ee6c3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77119593"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>Rövid útmutató: Luis-végpontkulcs beszerzése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt győződjön meg arról, hogy a következő elemeket kapta:

* Egy LUIS-fiók. Létrehozhat egy ingyenes fiókot a [LUIS portal](https://www.luis.ai/home) segítségével.

## <a name="luis-and-speech"></a>A LUIS és a beszéd

A LUIS integrálható a Speech Service szolgáltatással a beszédből való szándékfelismerés céljából. Ehhez nincs szükség Speech Service-előfizetésre, csak a LUIS-ra.

A LUIS háromféle kulcsot használ:

|Kulcs típusa|Cél|
|--------|-------|
|Tartalomkészítés|Lehetővé teszi a LUIS-alkalmazások programozási programozással történő létrehozását és módosítását|
|Kezdő|Lehetővé teszi a LUIS-alkalmazás tesztelését csak szöveggel|
|Végpont |Engedélyezi a hozzáférést egy adott LUIS-alkalmazáshoz|

Ebben az oktatóanyagban a végpontkulcs-típusra van szükség. Az oktatóanyag a példát használja a Home Automation LUIS alkalmazás, amely az [előre összeállított otthoni automatizálási alkalmazás rövid útmutatójának](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) követésével hozlétre. Ha saját LUIS-alkalmazást hozott létre, használhatja helyette.

Luis-alkalmazás létrehozásakor a LUIS automatikusan létrehoz egy kezdőkulcsot, így szöveges lekérdezések használatával tesztelheti az alkalmazást. Ez a kulcs nem engedélyezi a beszédszolgáltatás integrációját, és nem működik az oktatóanyag. Hozzon létre egy LUIS-erőforrást az Azure-irányítópulton, és rendelje hozzá a LUIS alkalmazáshoz. Az oktatóanyaghoz használhatja az ingyenes előfizetési szintet.

Miután létrehozta a LUIS-erőforrást az Azure-irányítópulton, jelentkezzen be a [LUIS-portálra,](https://www.luis.ai/home)válassza ki az alkalmazást a **Saját alkalmazások** lapon, majd váltson át az alkalmazás **Kezelése** lapjára. Végül válassza a **Kulcsok és végpontok elemet** az oldalsávon.

![A LUIS portál kulcs- és a végpontbeállításai](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

A **Kulcsok és végpontbeállításai** lapon:

1. Görgessen le az **Erőforrások és kulcsok** szakaszhoz, és válassza az Erőforrás **hozzárendelése lehetőséget.**
1. A **Kulcs hozzárendelése az alkalmazáshoz** párbeszédpanelen hajtsa végre a következő módosításokat:

   * A **Bérlő csoportban**válassza a **Microsoft**lehetőséget.
   * Az **Előfizetés neve csoportban**válassza ki a használni kívánt LUIS-erőforrást tartalmazó Azure-előfizetést.
   * A **Kulcs csoportban**válassza ki az alkalmazással használni kívánt LUIS-erőforrást.

   Az új előfizetés hamarosan megjelenik a lap alján található táblázatban.

1. A billentyű melletti ikonra jelölve másolja azt a vágólapra. (Bármelyik kulcsot használhatja.)

![A LUIS-app előfizetési kulcsai](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Leképezések felismerése](~/articles/cognitive-services/Speech-Service/quickstarts/intent-recognition.md)
