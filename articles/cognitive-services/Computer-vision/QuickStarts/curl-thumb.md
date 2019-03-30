---
title: 'Gyors útmutató: Hozzon létre egy miniatűr - REST, a cURL'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban miniatűrt hozhat létre egy képből a Computer Vision API és cURL használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 20a438f4eb932596647002bf9d3072c651ac969c
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630656"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-curl-in-computer-vision"></a>Gyors útmutató: A REST API-val miniatűrkép generálása és a cURL a Computer Vision

Ez a rövid útmutatóban egy rendszerképből számítógépes Látástechnológiai REST API használatával létrehozza a miniatűr. Megadhatja a kívánt magasságát és szélességét, amelyek eltérőek lehetnek a oldalarányát. a bemeneti lemezképből. Computer Vision segítségével intelligens vágása nyelvelemző, mind a terület hasznos helyek azonosításához, és hozzon létre körbevágási koordináták körül, adott régióban.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell a [cURL-lel](https://curl.haxx.se/windows).
- Szüksége lesz egy Computer Vision-előfizetői azonosítóra. Az előfizetői azonosító beszerzéséhez lásd az [előfizetői azonosítók beszerzéséről](../Vision-API-How-to-Topics/HowToSubscribe.md) szóló témakört.

## <a name="get-thumbnail-request"></a>Miniatűrbeszerzési kérés

A [Get Thumbnail metódussal](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) létrehozhatja egy kép miniatűrjét.

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

## <a name="create-and-run-the-sample-command"></a>A mintaparancs létrehozása és futtatása

A minta létrehozásához és futtatásához az alábbi lépéseket kell végrehajtania:

1. Másolja az alábbi parancsot egy szövegszerkesztőbe.
1. Hajtsa végre a következő módosításokat a parancs megfelelő területein:
    1. Cserélje le a `<subscriptionKey>` értéket az előfizetői azonosítóra.
    1. Cserélj le az `<thumbnailFile>` értékét azon fájl útvonalára és nevére, amelyben menteni szeretné a miniatűrt.
    1. Ha szükséges, cserélje le a kérés URL-címét (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail`) azon Azure-régió [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) metódusának végponti URL-címére, ahol az előfizetői azonosítókat beszerezte.
    1. Igény szerint cserélje a kép URL-címét a kérelem törzsében (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) egy másik olyan kép URL-címére, amelyből miniatűrt szeretne létrehozni.
1. Nyisson meg egy parancssort.
1. Illessze be a szövegszerkesztőből a parancsot, majd futtassa.

    ```console
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>A válasz vizsgálata

A sikeres válasz a(z) `<thumbnailFile>` helyen megszabott fájlba írja a miniatűrt. Ha a kérés meghiúsul, a válasz tartalmaz egy hibakódot és egy üzenetet, amely segít meghatározni a hiba okát. Ha a kérés úgy tűnik, hogy sikeres legyen, de a létrehozott miniatűr nem érvényes képfájl, lehet, hogy az előfizetési kulcs érvénytelen nem.

## <a name="next-steps"></a>További lépések

Ismerje meg a Computer Vision API, kép elemzése, hírességek és tereptárgyak felismerése észlelése, hozzon létre egy miniatűr és nyomtatott és kézzel írott szövegek kinyerése. A Computer Vision API-val való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Tekintse át a Computer Vision API-t](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
