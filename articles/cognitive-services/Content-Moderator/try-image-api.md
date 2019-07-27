---
title: Közepes méretű rendszerképek az API-konzollal – Content Moderator
titleSuffix: Azure Cognitive Services
description: Az Azure Content Moderatorban a képmoderálási API használatával kezdeményezheti a képtartalomhoz tartozó vizsgálati és felülvizsgálati moderálási munkafolyamatokat.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 20353d488cba255925d21017b77d1def2d06b30b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561243"
---
# <a name="moderate-images-from-the-api-console"></a>Mérsékelt rendszerképek az API-konzolról

Az Azure Content Moderatorban a képmoderálási [API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) használatával kezdeményezheti a képtartalomhoz tartozó vizsgálati és felülvizsgálati moderálási munkafolyamatokat. A moderálási feladatokban megvizsgálhatja a tartalmat a káromkodáshoz, és összehasonlítja az egyéni és a közös feketelistákkal.

## <a name="use-the-api-console"></a>Az API-konzol használata
Mielőtt tesztelni tudja az API-t az online konzolon, szüksége lesz az előfizetési kulcsra. Ez a **Beállítások** lap **OCP-APIM-előfizetés-Key** mezőjében található. További információkért lásd az [Áttekintést](overview.md).

1. Ugrás a [Képmoderálási API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)-referenciára.

   Megnyílik a **rendszerkép-** kiértékelési képmoderálás lap.

2. Az **Open API Testing Console**esetében válassza ki azt a régiót, amely a legszorosabban leírja a helyét. 

   ![Rendszerkép kipróbálása – kiértékelési oldal régiójának kiválasztása](images/test-drive-region.png)
  
   Megnyílik a **rendszerkép-kiértékelés API-** konzol.

3. Az **OCP-APIM-Subscription-Key** mezőbe írja be az előfizetési kulcsot.

   ![A rendszerkép kiértékelése – a konzol előfizetési kulcsa](images/try-image-api-1.PNG)

4. A **kérelem törzse** mezőben használja az alapértelmezett minta rendszerképet, vagy válasszon egy beolvasandó képet. A képet bináris bites adatokként is elküldheti, vagy megadhat egy képhez nyilvánosan elérhető URL-címet. 

   Ehhez a példához használja a **kérelem törzse** mezőben megadott elérési utat, majd válassza a **Küldés**lehetőséget. 

   ![Rendszerkép kiértékelése – a konzol kérésének törzse](images/try-image-api-2.PNG)

   Ez az URL-cím képe:

   ![A rendszerkép kiértékelése – példa képe](images/sample-image.jpg) 

5. Kattintson a **Küldés** gombra.

6. Az API az egyes besorolások valószínűségi pontszámát adja vissza. Azt is megadja, hogy a rendszerkép megfelel-e a feltételeknek (**igaz** vagy **hamis**). 

   ![A rendszerkép kiértékelése – a konzol valószínűségi pontszáma és a feltétel meghatározása](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Arcfelismerés

A képmoderálási API használatával megkeresheti az arcokat a képen. Ez a beállítás akkor lehet hasznos, ha adatvédelmi problémák merülnek fel, és meg szeretné akadályozni, hogy egy adott arc ne legyen közzétéve a platformon. 

1. A képmoderálási [API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)-referenciában, a bal oldali menüben, a **rendszerkép**területen válassza az **arcok keresése**lehetőséget. 

   Megnyílik a **rendszerkép-Find Faces** oldal.

2. Az **Open API Testing Console**esetében válassza ki azt a régiót, amely a legszorosabban leírja a helyét. 

   ![Próbálja ki a képkeresési arcok oldal régiójának kijelölését](images/test-drive-region.png)

   Ekkor megnyílik a **rendszerkép-Find Faces API-** konzol.

3. Itt adhatja meg a beolvasandó képet. A képet bináris bites adatként is elküldheti, vagy megadhat egy nyilvánosan elérhető URL-címet a képhez. Ez a példa egy CNN-történetben használt képre mutat.

   ![Rendszerkép keresése – minta képe](images/try-image-api-face-image.jpg)

   ![Rendszerkép keresése – példa kérésre](images/try-image-api-face-request.png)

4. Kattintson a **Küldés** gombra. Ebben a példában az API két arcot keres, és visszaadja a képen látható koordinátákat.

   ![Próbálja ki a képkeresési példákat](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Szöveg észlelése OCR-képesség használatával

A képekben lévő szöveg észleléséhez használhatja a Content Moderator OCR funkciót.

1. A képmoderálási [API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)-referenciában, a bal oldali menüben, a **rendszerkép**területen válassza az **OCR**elemet. 

   Megnyílik a **rendszerkép – OCR** oldal.

2. Az **Open API Testing Console**esetében válassza ki azt a régiót, amely a legszorosabban leírja a helyét. 

   ![Rendszerkép – OCR-oldal régiójának kiválasztása](images/test-drive-region.png)

   Megnyílik a **rendszerkép – OCR API-** konzol.

3. Az **OCP-APIM-Subscription-Key** mezőbe írja be az előfizetési kulcsot.

4. A **kérelem törzse** mezőben használja az alapértelmezett minta képét. Ez ugyanaz a rendszerkép, amelyet az előző szakaszban is használni kell.

5. Kattintson a **Küldés** gombra. A kinyert szöveg a JSON-ban jelenik meg:

   ![Rendszerkép – OCR-minta válaszának tartalma mező](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>További lépések

Használja az REST API a kódban, vagy Kezdje a képmoderálási [.net](image-moderation-quickstart-dotnet.md) gyors útmutatóval az alkalmazással való integrációhoz.
