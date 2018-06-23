---
title: Tartalom mérsékelt Azure tartalom moderátor emberi értékelést használatával |} Microsoft Docs
description: 'Útmutató: a tartalom moderátor API konzolban emberi értékelést létrehozásához.'
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: e9faf595e65ba4475a743e4cb45919fd30fbd6e8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347003"
---
# <a name="create-reviews-from-the-api-console"></a>Az API-konzolról értékelést létrehozása

Az át API [tekintse át a műveletek](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4) emberi moderálás a kép vagy szöveg értékelést létrehozásához. Emberi moderátorok használja a a felülvizsgálati eszköz tekintse át a tartalmat. Használja ezt a műveletet a utáni moderálás üzleti logika alapján. Használja azt követően, rendelkezik a tartalom a tartalom moderátor kép vagy szöveges API-k és más kognitív szolgáltatások API-k használatával. 

Miután emberi Moderátorra ellenőrzi, hogy az automatikusan hozzárendelt címkék és előrejelzési adatokat, és elküldi a végleges moderálás döntést, tekintse át az API-t minden információt az API-végpont küldi el.

## <a name="use-the-api-console"></a>Az API-konzollal
Az API-t test-drive az online konzol használatával, kell néhány értékeket kell megadnia a konzolhoz:

- **teamName**: A csoport nevére, amelyet a felülvizsgálati eszköz fiók beállításakor hozott létre. 
- **ContentId**: Ez a karakterlánc az API-nak átadott, és vissza a visszahívás keresztül. A ContentId esetén hasznos belső azonosítók vagy metaadatok társít egy moderálás feladat eredményeinek.
- **Metaadatok**: egyéni kulcs-érték párok visszakerül az API-végpont a visszahívás során. Ha a kulcs egy rövid kódot, amely a felülvizsgálati eszköz van megadva, a kód jelenik meg.
- **Az OCP-Apim-előfizetés-kulcs**: található a **beállítások** fülre. További információkért lásd: [áttekintése](overview.md).

Egy tesztelési konzol eléréséhez a legegyszerűbb származik a **hitelesítő adatok** ablak.

1.  Az a **hitelesítő adatok** ablakban válassza ki [felülvizsgálati API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

  A **áttekintése – létrehozása** lap megnyitásakor.

2.  A **nyílt API-t tesztelési konzol**, válassza ki azt a régiót, amely a leginkább leírja a tartózkodási helyét.

  ![Tekintse át, – létrehozása lap terület kiválasztása](images/test-drive-region.png)

  A **áttekintése – létrehozása** API-konzol megnyitása.
  
3.  Adja meg a kötelező lekérdezési paraméterek, tartalomtípus és az Előfizetés kulcs értékét. Az a **Request body** mezőben adja meg a tartalmat (például kép), a metaadatok és a más információk is társítva vannak a tartalmat.

  ![Tekintse át,-konzol lekérdezési paraméterek, a fejlécek és a kérelem törzsében mezőbe létrehozása](images/test-drive-review-1.PNG)
  
4.  Kattintson a **Küldés** gombra. A felülvizsgálati ID jön létre. Másolja le az Azonosítót a következő lépések használatához.

  ![Tekintse át,-konzol válasz tartalom jelenít meg a felülvizsgálati azonosító létrehozása](images/test-drive-review-2.PNG)
  
5.  Válassza ki **beolvasása**, majd nyissa meg az API-t a gomb, amely megfelel a régió kiválasztásával. Az eredményül kapott oldalon adja meg az értékeket a **teamName**, **ReviewID**, és **előfizetés kulcs**. Válassza ki a **küldése** gombra a lapon. 

  ![Tekintse át,-konzol Get eredmények létrehozása](images/test-drive-review-3.PNG)
  
6.  Látni fogja a vizsgálat eredményeit.

  ![Tekintse át,-konzol válasz tartalom mezőben létrehozása](images/test-drive-review-4.PNG)
  
7.  A tartalom moderátor irányítópultra, válassza ki a **felülvizsgálati** > **kép**. A vizsgált jelenik meg, és készen áll a emberi áttekintése.

  ![Tekintse át a focilabdát eszköz képe](images/test-drive-review-5.PNG)

## <a name="next-steps"></a>További lépések

A REST API-t használja a kódban, vagy indítsa el a [értékelést .NET gyors üzembe helyezés](moderation-reviews-quickstart-dotnet.md) integrálása az alkalmazást.
