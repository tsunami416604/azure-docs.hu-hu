---
title: Tartalommoderálás, emberi ellenőrző használatával az Azure Content Moderator |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre az emberi ellenőrző a Content Moderator API-konzolon.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 214695ed3e23d1f501d6d4691104b3f8a91f6efc
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866508"
---
# <a name="create-reviews-from-the-api-console"></a>Értékelések készítése az API-konzol

A tekintse át API [tekintse át az operations](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4) emberi moderálás képet vagy szöveget felülvizsgálatának létrehozásához. Az emberi moderátorok a felülvizsgálati eszköz használatával tekintse át a tartalmat. Használja ezt a műveletet a jóváhagyás utáni üzleti logika alapján. Azt követően, rendelkezik a tartalom a Content Moderator kép vagy a szöveges API-k, illetve egyéb Cognitive Services API-k egyikével használja. 

Miután egy emberi moderátor ellenőrzi a hozzárendelt címkék és előrejelzési adatokat, és elküldi a végső jóváhagyás döntés, a felülvizsgálati API az API-végpont összes információt küldi el.

## <a name="use-the-api-console"></a>Az API-konzol használata
Próbálhatják ki őket az API-t az online konzol használatával, meg kell adnia a konzol néhány értékek van szükség:

- **teamName**: A csoport nevére, a felülvizsgálati eszköz fiók beállításakor hozott létre. 
- **ContentId**: Ez a karakterlánc az API-nak átadott és a visszahívás keresztül visszaadott. A ContentId hasznos belső azonosítók vagy metaadat társít egy moderálás feladat eredményét.
- **Metaadatok**: az API-végpont egyéni kulcs-érték párok vissza a visszahívás során. Ha a kulcs egy rövid kód a vizsgálóeszközt definiált, egy címke jelenik meg.
- **OCP-Apim-Subscription-Key**: található a **beállítások** fülre. További információkért lásd: [áttekintése](overview.md).

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
