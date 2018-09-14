---
title: Miniatűr beszerzése a Computer Vision API és a cURL használatával – rövid útmutató | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Ebben a rövid útmutatóban miniatűrt fog létrehozni egy képből a Computer Vision és a cURL használatával a Cognitive Servicesben.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 4056f05f3f4bf97761f683b5f3a9053666d4ea26
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43770113"
---
# <a name="quickstart-generate-a-thumbnail---rest-curl"></a>yRövid útmutató: Miniatűr létrehozása – REST, cURL

Ebben a rövid útmutatóban miniatűrt fog létrehozni egy képből a Computer Vision segítségével.

## <a name="prerequisites"></a>Előfeltételek

A Computer Vision használatához előfizetési kulcsra van szüksége, lásd az [előfizetési kulcsok beszerzéséről](../Vision-API-How-to-Topics/HowToSubscribe.md) szóló témakört.

## <a name="get-thumbnail-request"></a>Miniatűrbeszerzési kérés

A [Get Thumbnail metódussal](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) létrehozhatja egy kép miniatűrjét. Megadhatja a magasságát és a szélességét, amely eltérhet a bemeneti kép oldalarányától. A Computer Vision intelligens körbevágással azonosítja a releváns régiót, amely alapján létrehozza a vágási koordinátákat.

A minta futtatásához az alábbi lépéseket kell végrehajtania:

1. Másolja az alábbi kódot egy szerkesztőbe.
1. A `<Subscription Key>` helyére írja be az érvényes előfizetési kulcsot.
1. A `<File>` helyére írja be a miniatűr mentési útvonalát és fájlnevét.
1. Ha szükséges, változtassa meg a kérés URL-címét (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) arra a címre, ahonnan beszerezte az előfizetési kulcsot.
1. Az elemezni kívánt képet (`{\"url\":\"...`) is módosíthatja.
1. Nyisson meg egy parancssorablakot egy számítógépen, amelyen a cURL telepítve van.
1. Másolja be a kódot, és futtassa a parancsot.

>[!NOTE]
>A REST-hívásban ugyanazt a helyet kell használnia, ahonnan az előfizetési kulcsot beszerezte. Ha például a westus régióból szerezte be az előfizetési kulcsot, cserélje le az alábbi URL-címben látható „westcentralus” elemet a „westus” elemre.

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -o <File> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
```

## <a name="get-thumbnail-response"></a>A miniatűrbeszerzés válasza

A sikeres válasz a `<File>` fájlba írja a miniatűrt. Ha a kérés meghiúsul, a válasz tartalmaz egy hibakódot és egy üzenetet, amely segít meghatározni a hiba okát.

## <a name="next-steps"></a>További lépések

Ismerje meg a Computer Vision API-kat, amelyekkel képeket elemezhet, hírességeket és nevezetességeket azonosíthat rajtuk, valamint miniatűrt hozhat létre, illetve nyomtatott és kézzel írott szövegeket nyerhet ki belőlük. A Computer Vision API-kkal való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Ismerkedjen meg a Computer Vision API-k működésével](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
