---
title: Képek moderálása az API-konzollal – Tartalommoderátor
titleSuffix: Azure Cognitive Services
description: Az Azure Content Moderator képmoderálási API-jával kezdeményezheti a képtartalom be- és ellenőrzési moderálási munkafolyamatait.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 714621fdcc307ee8b29567fc0d95ca41d31aa9e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75448261"
---
# <a name="moderate-images-from-the-api-console"></a>Mérsékelt képek az API-konzolról

Az Azure Content Moderator [képmoderálási API-jával](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) kezdeményezheti a képtartalom be- és ellenőrzési moderálási munkafolyamatait. A moderálási feladat megvizsgálja a tartalmat a káromkodás, és összehasonlítja az egyéni és megosztott tiltólisták.

## <a name="use-the-api-console"></a>Az API-konzol használata
Mielőtt tesztelheti az API-t az online konzolon, szüksége van az előfizetési kulcsra. Ez a **Beállítások** lapon, az **Ocp-Apim-Subscription-Key** mezőben található. További információkért lásd az [Áttekintést](overview.md).

1. Nyissa meg a [Képmoderálás API-hivatkozását.](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)

   Megnyílik a Kép - Képmoderálás **kiértékelése** lap.

2. Az **Open API tesztelési konzol,** válassza ki a régiót, amely a legszorosabban leírja a helyét. 

   ![Kép kipróbálása – Az oldalterület kiválasztásának kiértékelése](images/test-drive-region.png)
  
   Megnyílik **a Image - Evaluate** API console.

3. Az **Ocp-Apim-Subscription-Key** mezőbe írja be az előfizetési kulcsot.

   ![Kép kipróbálása – Konzol-előfizetési kulcs kiértékelése](images/try-image-api-1.PNG)

4. A **Testtest kérése** mezőben használja az alapértelmezett mintaképet, vagy adjon meg egy bekéseléshez használt képet. Magát a képet elküldheti bináris bitadatként, vagy megadhat egy nyilvánosan elérhető URL-címet egy képhez. 

   Ebben a példában használja a **Kérelem törzsmezőben** megadott elérési utat, majd válassza a **Küldés lehetőséget.** 

   ![Kép kipróbálása - A konzolkérelem törzsének kiértékelése](images/try-image-api-2.PNG)

   Ez a kép az URL-címen:

   ![Kép kipróbálása - A konzol mintaképének kiértékelése](images/sample-image.jpg) 

5. Válassza a **Küldés**lehetőséget.

6. Az API minden besoroláshoz egy valószínűségi pontszámot ad vissza. Azt is visszaadja annak meghatározását, hogy a kép megfelel-e a feltételeknek **(igaz** vagy **hamis).** 

   ![Kép kipróbálása – A konzol valószínűségi pontszámának és állapotmeghatározásának értékelése](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Arcfelismerés

A Képmoderálás API segítségével megkeresheti a képek en lévő lapokat. Ez a beállítás akkor lehet hasznos, ha adatvédelmi aggályai vannak, és meg szeretné akadályozni, hogy egy adott arcot közzétegyenek a platformon. 

1. A [Képmoderálás API hivatkozásában](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)a bal oldali kép **kép**területén válassza az **Arcok keresése**lehetőséget. 

   Megnyílik **a Kép - Arcok keresése** lap.

2. Az **Open API tesztelési konzol,** válassza ki a régiót, amely a legszorosabban leírja a helyét. 

   ![Kép kipróbálása – Az arcok oldalterület kiválasztása](images/test-drive-region.png)

   Megnyílik **a Image - Find Faces** API-konzol.

3. Adja meg a beszkavalkadó képet. Magát a képet elküldheti bináris bitadatként, vagy megadhat egy nyilvánosan elérhető URL-címet egy képhez. Ez a példa egy CNN-történetben használt képre mutat.

   ![Próbálja ki a képet - Arcok keresése mintakép](images/try-image-api-face-image.jpg)

   ![Próbálja ki a képet - Arcok keresése mintakérelem](images/try-image-api-face-request.png)

4. Válassza a **Küldés**lehetőséget. Ebben a példában az API két arcot talál, és visszaadja a koordinátáikat a képen.

   ![Kép kipróbálása – Arcok keresése választartalom-tartalommező](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Szövegfelismerés OCR-képességgel

A Tartalommoderátor OCR-funkcióval észlelheti a képeken lévő szöveget.

1. A [Képmoderálás API hivatkozásában](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)a bal oldali menü **Kép**területén válassza az **OCR**lehetőséget. 

   Megnyílik **a Kép - OCR** lap.

2. Az **Open API tesztelési konzol,** válassza ki a régiót, amely a legszorosabban leírja a helyét. 

   ![Kép – OCR-oldalterület kiválasztása](images/test-drive-region.png)

   Megnyílik **a Image - OCR** API konzol.

3. Az **Ocp-Apim-Subscription-Key** mezőbe írja be az előfizetési kulcsot.

4. A **Kérelem törzsmezőben** használja az alapértelmezett mintaképet. Ez ugyanaz a lemezkép, amelyet az előző szakaszban is használtak.

5. Válassza a **Küldés**lehetőséget. A kibontott szöveg a JSON-ban jelenik meg:

   ![Kép – OCR mintaválasz-tartalom mező](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>További lépések

Használja a REST API-t a kódban, vagy kövesse a [.NET SDK gyorsindítást,](dotnet-sdk-quickstart.md) és adja hozzá a képmoderálást az alkalmazáshoz.
