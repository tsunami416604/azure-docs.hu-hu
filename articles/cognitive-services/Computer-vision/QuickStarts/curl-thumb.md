---
title: 'yRövid útmutató: Miniatűr létrehozása – REST, cURL'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban miniatűrt hozhat létre egy képből a Computer Vision API és cURL használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 1c2ab3f0ef84007d8b9233c45d39fcfbacb5eaab
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835306"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>Gyors útmutató: miniatűr létrehozása a Computer Vision REST API és a cURL használatával

Ebben a rövid útmutatóban létrehoz egy miniatűrt a rendszerképből a Computer Vision REST API használatával. Megadhatja a kívánt magasságot és szélességet, ami eltérő lehet a bemeneti kép oldalarányának arányában. A Computer Vision az intelligens levágás használatával intelligens módon azonosítja az érdeklődési területet, és az adott régió körüli koordinátákat állít elő.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/) 
* [cURL](https://curl.haxx.se/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" hozzon létre egy Computer Vision erőforrást, "  target="_blank"> és hozzon létre egy Computer Vision-erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése**elemre.
  * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Computer Vision szolgáltatáshoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
  * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="create-and-run-the-sample-command"></a>A mintaparancs létrehozása és futtatása

A minta létrehozásához és futtatásához az alábbi lépéseket kell végrehajtania:

1. Másolja az alábbi parancsot egy szövegszerkesztőbe.
1. Hajtsa végre a következő módosításokat a parancs megfelelő területein:
    1. Cserélje le a `<subscriptionKey>` értéket az előfizetői azonosítóra.
    1. Cserélj le az `<thumbnailFile>` értékét azon fájl útvonalára és nevére, amelyben menteni szeretné a miniatűrt.
    1. Cserélje le a kérelem URL-címének ( `westcentralus` ) első részét a saját végpont URL-címében található szövegre.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Igény szerint cserélje a kép URL-címét a kérelem törzsében (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) egy másik olyan kép URL-címére, amelyből miniatűrt szeretne létrehozni.
1. Nyisson meg egy parancsablakot.
1. Illessze be a parancsot a szövegszerkesztőbe a parancssori ablakba.
1. A program futtatásához nyomja le az ENTER billentyűt.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>A válasz vizsgálata

A sikeres válasz a(z) `<thumbnailFile>` helyen megszabott fájlba írja a miniatűrt. Ha a kérés meghiúsul, a válasz tartalmaz egy hibakódot és egy üzenetet, amely segít meghatározni a hiba okát. Ha a kérelem sikeresnek tűnik, de a létrehozott miniatűr nem érvényes képfájl, előfordulhat, hogy az előfizetési kulcs érvénytelen.

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a Computer Vision APIával, hogyan elemezheti a rendszerképeket, azonosíthatja a hírességeket és tereptárgyait, miniatűrt hozhat létre, és kinyerheti a nyomtatott és a kézzel írott A Computer Vision API-val való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c).

> [!div class="nextstepaction"]
> [Tekintse át a Computer Vision API-t](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f21b)
