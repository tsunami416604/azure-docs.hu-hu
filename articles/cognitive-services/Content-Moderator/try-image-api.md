---
title: Közepes szintű Azure tartalom moderátor képekkel |} Microsoft Docs
description: Test-Drive kép moderálás a tartalom moderátor API-konzolon.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: fec54826c70ae10e56c68406f629c56639985295
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347058"
---
# <a name="moderate-images-from-the-api-console"></a>Az API-konzolról mérsékelt lemezképek

Használja a [kép moderálás API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) Azure tartalom moderátor kezdeményezése ellenőrzés és áttekintés moderálás munkafolyamatainak kép tartalmához. A moderálás feladat ellenőrzi a tartalom a profán kifejezéseket, és egyéni és megosztott feketelistákon összevetett.

## <a name="use-the-api-console"></a>Az API-konzollal
Az API-nak az online konzolon is test-drive, meg kell az Előfizetés-kulcs. Ez a található a **beállítások** lap a **Ocp-Apim-előfizetés-kulcs** mezőbe. További információkért lásd: [áttekintése](overview.md).

1.  Ugrás a [kép moderálás API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

  A **kép – kiértékelése** kép moderálás oldal megnyitása.

2. A **nyílt API-t tesztelési konzol**, válassza ki azt a régiót, amely a leginkább leírja a tartózkodási helyét. 

  ![Próbálja meg a lemezkép - kiértékelése terület kiválasztása lap](images/test-drive-region.png)
  
  A **kép – kiértékelése** API-konzol megnyitása.

3. Az a **Ocp-Apim-előfizetés-kulcs** mezőbe írja be az Előfizetés-kulcs.

  ![Próbálja meg a lemezkép - konzol előfizetés kulcs kiértékelése](images/try-image-api-1.PNG)

4. Az a **Request body** mezőben használja az alapértelmezett minta lemezképet, vagy adjon meg egy kép vizsgálata. Elküldheti a magát a képet bináris adatok bit, vagy adjon meg egy kép nyilvánosan elérhető URL-címet. 

  Ebben a példában a megadott elérési utat használja a **Request body** mezőbe, majd válassza ki **küldése**. 

   ![Próbálja meg a lemezkép - konzol kérelemtörzset kiértékelése](images/try-image-api-2.PNG)

  Ez az URL-címet a képnek:

  ![Próbálja meg a lemezkép - kép: a minta konzol kiértékelése](images/sample-image.jpg) 

5. Kattintson a **Küldés** gombra.

6. Az API-t adja vissza az egyes kategóriákban valószínűségét jelző pontszámot. Is meghatározására, hogy a lemezképet a feltételeknek megfelelő adja vissza (**igaz** vagy **hamis**). 

  ![Próbálja meg a lemezkép - konzol valószínűség pontszám kiértékelheti és feltétel meghatározása](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Arcfelismerés

A kép moderálás API segítségével keresse meg a lap a kép. Ez a beállítás akkor lehet hasznos, ha adatvédelmi megfontolások, és szeretné akadályozni, hogy egy adott arc a platformon elküldését. 

1.  Az a [kép moderálás API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), a bal oldali menü alatt **kép**, jelölje be **keresése előtt álló**. 

  A **lemezkép - keresése előtt álló** lap megnyitásakor.

2.  A **nyílt API-t tesztelési konzol**, válassza ki azt a régiót, amely a leginkább leírja a tartózkodási helyét. 

  ![Próbálja meg a lemezkép - található lapok lap terület kiválasztása](images/test-drive-region.png)

  A **lemezkép - keresése előtt álló** API-konzol megnyitása.

3. Adjon meg egy kép vizsgálata. Elküldheti a magát a képet bináris adatok bit, vagy adja meg a lemezkép nyilvánosan elérhető URL-CÍMÉT. Ez a példa a CNN szövegegység használt kép mutató hivatkozásokat tartalmaz.

  ![Próbálja meg a lemezkép - lapok minta lemezkép keresése](images/try-image-api-face-image.jpg)

  ![Próbálja meg a lemezkép - lapok kérelemmintát keresése](images/try-image-api-face-request.png)

4. Kattintson a **Küldés** gombra. Ebben a példában az API-t két oldalát talál, és adja vissza a koordináták a lemezképet.

   ![Próbálja meg a lemezkép - lapok minta válasz tartalom mezőben található](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Szöveg észlelési OCR képességeivel

A tartalom moderátor OCR funkció segítségével azonosíthatók a szöveg képek.

1. Az a [kép moderálás API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), a bal oldali menü alatt **kép**, jelölje be **OCR**. 

  A **lemezkép - OCR** lap megnyitásakor.

2. A **nyílt API-t tesztelési konzol**, válassza ki azt a régiót, amely a leginkább leírja a tartózkodási helyét. 

  ![Lemezkép - OCR lap terület kiválasztása](images/test-drive-region.png)

  A **lemezkép - OCR** API-konzol megnyitása.

3. Az a **Ocp-Apim-előfizetés-kulcs** mezőbe írja be az Előfizetés-kulcs.

4. Az a **Request body** , akkor az alapértelmezett minta lemezképet. Ez a ugyanazt a képet, amely az előző szakaszban leírt szolgál.

5. Kattintson a **Küldés** gombra. A kibontott szöveg JSON jelenik meg:

  ![Lemezkép - OCR minta válasz tartalom mezőben](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>További lépések

A REST API-t használja a kódban, vagy indítsa el a [kép moderálás .NET gyors üzembe helyezés](image-moderation-quickstart-dotnet.md) integrálása az alkalmazást.
