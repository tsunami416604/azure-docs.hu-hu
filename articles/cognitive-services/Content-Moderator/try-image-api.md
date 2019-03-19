---
title: Az API-konzol - Content Moderator mérsékelt rendszerképek
titlesuffix: Azure Cognitive Services
description: A kép moderálási API-t használja az Azure Content Moderator kép tartalmához munkafolyamatainak vizsgálat és felülvizsgálati jóváhagyás kezdeményezése.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 1e4efa5e06525194bfdc7d1932fcfec5ec9f8c6b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58093820"
---
# <a name="moderate-images-from-the-api-console"></a>Az API-konzolról mérsékelt képek

Használja a [Image moderálási API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) az Azure Content Moderator kezdeményezni, ellenőrzés és áttekintés moderálás munkafolyamatokat a kép tartalmához. A moderálás feladat megkeresi a trágárság cenzúrázása a tartalmat, és megosztott és az egyéni feketelistákkal összevetett.

## <a name="use-the-api-console"></a>Az API-konzol használata
Az API az online konzolon is próbálhatják ki őket, meg kell az előfizetési kulcs. Ez található a **beállítások** lap a **Ocp-Apim-Subscription-Key** mezőbe. További információkért lásd az [Áttekintést](overview.md).

1. Lépjen a [kép moderálási API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

   A **kép – kiértékelése** kép moderálás oldal megnyílik.

2. A **Open API tesztelési konzollal**, válassza ki a régiót, amelyben leginkább a tartózkodási ismerteti. 

   ![Próbálja meg a lemezkép - kiértékelése lap régió kiválasztása](images/test-drive-region.png)
  
   A **kép – kiértékelése** API-konzol megnyitása.

3. Az a **Ocp-Apim-Subscription-Key** adja meg az előfizetési kulcs.

   ![Próbálja meg a lemezkép - konzol az előfizetői kiértékelése](images/try-image-api-1.PNG)

4. Az a **kérelem törzse** mezőben használja az alapértelmezett képet, vagy adja meg a lemezkép beolvasása. Küldhet magának a kép bináris adatok bit, vagy adja meg a kép egy nyilvánosan elérhető URL-címet. 

   Ebben a példában használja a megadott elérési úton a **kérelem törzse** mezőbe, majd válassza ki **küldése**. 

   ![Próbálja meg a lemezkép - konzol kéréstörzs kiértékelése](images/try-image-api-2.PNG)

   Ez az a képet a megadott URL-címen:

   ![Próbálja meg a lemezkép - konzol képet kiértékelése](images/sample-image.jpg) 

5. Kattintson a **Küldés** gombra.

6. Az API-t a valószínűségi pontszámának létrehozásához minden egyes adja vissza. Emellett meghatározására, hogy a lemezkép megfelel-e a adja vissza (**igaz** vagy **hamis**). 

   ![Próbálja meg a lemezkép - konzol valószínűségi pontszámának kiértékelése és feltételek meghatározása](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Arcfelismerés

A kép moderálási API segítségével keresse meg az arcok a képen. Ez a beállítás akkor hasznos, ha adatvédelmi aggályokat, és szeretné, hogy egy adott arc csökkentheti a közzétett platformon. 

1. Az a [kép moderálási API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), a bal oldali menüben alatt **kép**, jelölje be **található arcokat**. 

   A **kép – keresés arcokat** lap megnyitásakor.

2. A **Open API tesztelési konzollal**, válassza ki a régiót, amelyben leginkább a tartózkodási ismerteti. 

   ![Próbálja ki a kép – keresse meg az arcok lap régió kiválasztása](images/test-drive-region.png)

   A **kép – keresés arcokat** API-konzol megnyitása.

3. Adja meg a lemezkép beolvasása. Maga a kép bináris küldhet adatokat bit, vagy egy nyilvánosan elérhető-e egy képre mutató URL-címet. Ez a példa egy CNN történetet használt kép mutató hivatkozásokat tartalmaz.

   ![Próbálja meg a lemezkép - képet arcok keresése](images/try-image-api-face-image.jpg)

   ![Próbálja meg a lemezkép - mintakérelem arcok keresése](images/try-image-api-face-request.png)

4. Kattintson a **Küldés** gombra. Ebben a példában az API-t két arc keresése, és adja vissza a koordináták a képen.

   ![Próbálja meg a lemezkép - arcok minta válasz tartalmú panelen található](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Optikai Karakterfelismerés képességeivel szöveg észlelése

A Content Moderator optikai Karakterfelismerés funkció segítségével a képeken található rendszerképek szöveget.

1. Az a [kép moderálási API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), a bal oldali menüben alatt **kép**, jelölje be **OCR**. 

   A **lemezkép - OCR** lap megnyitásakor.

2. A **Open API tesztelési konzollal**, válassza ki a régiót, amelyben leginkább a tartózkodási ismerteti. 

   ![Lemezkép - OCR lap régió kiválasztása](images/test-drive-region.png)

   A **lemezkép - OCR** API-konzol megnyitása.

3. Az a **Ocp-Apim-Subscription-Key** adja meg az előfizetési kulcs.

4. Az a **kérelem törzse** használja az alapértelmezett képet. Ez az az előző szakaszban használt ugyanazt a lemezképet.

5. Kattintson a **Küldés** gombra. A JSON-ban a kinyert szöveg jelenik meg:

   ![Lemezkép - OCR minta válasz tartalmú panelen](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>További lépések

A REST API használata a kódban, vagy kezdje a [kép moderálás .NET – rövid útmutató](image-moderation-quickstart-dotnet.md) integrálhatja az alkalmazást.
