---
title: Az API-konzol - Content Moderator az emberi ellenőrző mérsékelt tartalom
titlesuffix: Azure Cognitive Services
description: Használja a tekintse át API tekintse át az operations emberi moderálás képet vagy szöveget felülvizsgálatának létrehozásához.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 7e4c750e7470c6a76e56bd67d6a134b1b61535eb
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218390"
---
# <a name="create-reviews-from-the-api-console"></a>Értékelések készítése az API-konzol

A tekintse át API [tekintse át az operations](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4) emberi moderálás képet vagy szöveget felülvizsgálatának létrehozásához. Az emberi moderátorok a felülvizsgálati eszköz használatával tekintse át a tartalmat. Használja ezt a műveletet a jóváhagyás utáni üzleti logika alapján. Azt követően, rendelkezik a tartalom a Content Moderator kép vagy a szöveges API-k, illetve egyéb Cognitive Services API-k egyikével használja. 

Miután egy emberi moderátor ellenőrzi a hozzárendelt címkék és előrejelzési adatokat, és elküldi a végső jóváhagyás döntés, a felülvizsgálati API az API-végpont összes információt küldi el.

## <a name="use-the-api-console"></a>Az API-konzol használata
Próbálhatják ki őket az API-t az online konzol használatával, meg kell adnia a konzol néhány értékek van szükség:

- **teamName**: A felülvizsgálati eszköz fiók beállítása során létrehozott csoport neve. 
- **ContentId**: Ez a karakterlánc az API-nak átadott és a visszahívás keresztül visszaadott. A ContentId hasznos belső azonosítók vagy metaadat társít egy moderálás feladat eredményét.
- **metaadatok**: Egyéni kulcs-érték párok vissza az API-végpont a visszahívás során. Ha a kulcs egy rövid kód a vizsgálóeszközt definiált, egy címke jelenik meg.
- **Ocp-Apim-Subscription-Key**: Található a **beállítások** fülre. További információkért lásd az [Áttekintést](overview.md).

A legegyszerűbb módszer a tesztelési konzol eléréséhez, az a **hitelesítő adatok** ablak.

1.  Az a **hitelesítő adatok** ablakban válassza [felülvizsgálati API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

  A **áttekintése – létrehozása** lap megnyitásakor.

2.  A **Open API tesztelési konzollal**, válassza ki a régiót, amelyben leginkább a tartózkodási ismerteti.

  ![Tekintse át, – létrehozása lap régió kiválasztása](images/test-drive-region.png)

  A **áttekintése – létrehozása** API-konzol megnyitása.
  
3.  Adja meg a szükséges lekérdezési paramétereket, tartalom típusa és az előfizetési kulcs értékét. Az a **kérelem törzse** adja meg a tartalom (például lemezkép helyét), a metaadatok és az egyéb a tartalommal kapcsolatos információkat.

  ![Tekintse át,-konzol lekérdezési paramétereket, fejléceket és kérelem törzse mezőbe létrehozása](images/test-drive-review-1.PNG)
  
4.  Kattintson a **Küldés** gombra. A felülvizsgálati ID jön létre. Másolja ezt az Azonosítót a következő lépésekben használni.

  ![Tekintse át,-konzol válasz content jelenít meg a felülvizsgálati azonosító létrehozása](images/test-drive-review-2.PNG)
  
5.  Válassza ki **első**, majd nyissa meg az API-t, amely megfelel az Ön régiójában gomb kiválasztásával. Az eredményül kapott lapon adja meg a **teamName**, **ReviewID**, és **előfizetési kulcs**. Válassza ki a **küldése** gombra a lapon. 

  ![Tekintse át – a Get-eredményeket hozhatnak létre a konzol](images/test-drive-review-3.PNG)
  
6.  Látni fogja a vizsgálat eredményeit.

  ![Tekintse át – a konzol válasz tartalom mező létrehozása](images/test-drive-review-4.PNG)
  
7.  A Content Moderator irányítópultján válassza ki a **felülvizsgálati** > **lemezkép**. A vizsgált kép akkor jelenik meg, emberi felülvizsgálati kész.

  ![Tekintse át a foci golyó eszköz képe](images/test-drive-review-5.PNG)

## <a name="next-steps"></a>További lépések

A REST API használata a kódban, vagy kezdje a [felülvizsgálatok .NET – rövid útmutató](moderation-reviews-quickstart-dotnet.md) integrálhatja az alkalmazást.
