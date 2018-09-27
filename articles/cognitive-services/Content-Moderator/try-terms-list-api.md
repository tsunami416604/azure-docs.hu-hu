---
title: Mérsékelt SMS-t egyéni kifejezéslisták - Content Moderator
titlesuffix: Azure Cognitive Services
description: A Content Moderator API konzolon egyéni kifejezéslisták kipróbálhassák azt.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 99df9fda2cc56f169a61ec215a976de28fc13d27
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220278"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Üzletmenetre gyakorolt közepes az egyéni kifejezéslisták az API-konzolon

Az alapértelmezett globális listáját az Azure Content Moderator feltételeket is elegendő legtöbb tartalom moderálása igényei szerint. Azonban szüksége lehet a szervezet adott feltételek képernyőn. Érdemes például versenytárs címkenevek további ellenőrzésre. 

Használja a [lista felügyeleti API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) egyéni listák a moderálási API használata a feltételek létrehozásához. A **szöveg – a képernyő** művelet megkeresi a trágárság cenzúrázása a szöveget, és megosztott és az egyéni feketelistákkal összeveti is összehasonlítja.

> [!NOTE]
> A maximális korlát **5 kifejezés sorolja fel** az egyes lista **nem haladhatja meg a 10 000 feltételek**.
>

A lista Management API segítségével a következő feladatokat végezheti el:
- Hozzon létre egy listát.
- Feltételek hozzáadása a listához.
- Képernyőn elleni egy lista a használati feltételeket.
- Feltételek törlése egy listáról.
- Egy lista törölhető.
- Lista adatainak szerkesztése.
- Frissítse az index, hogy a lista módosításainak egy új vizsgálatot szerepelnek.

## <a name="use-the-api-console"></a>Az API-konzol használata

Az API az online konzolon is próbálhatják ki őket, meg kell az előfizetési kulcs. Ez a kulcs található a **beállítások** lap a **Ocp-Apim-Subscription-Key** mezőbe. További információkért lásd: [áttekintése](overview.md).

## <a name="refresh-search-index"></a>Frissítse a search-index

A kifejezéslista módosítása után frissítenie kell az index későbbi vizsgálataiba szereplő módosítások. Ebben a lépésben a hasonló hogyan egy keresőmotor, az asztalon (Ha engedélyezve van) és a egy webes keresőmotor folyamatosan frissíti új fájlok és lapok indexét.

1.  Az a [kifejezés lista felügyeleti API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), a bal oldali menüben válassza ki a **kifejezés sorolja fel**, majd válassza ki **Search-Index frissítése**. 

  A **kifejezés listázza - Search-Index frissítése** lap megnyitásakor.

2. A **Open API tesztelési konzollal**, válassza ki a régiót, amelyben leginkább a tartózkodási ismerteti. 

  ![Kifejezéslisták - frissítés Search-Index lapot régió kiválasztása](images/test-drive-region.png)

  A **kifejezés listázza - Search-Index frissítése** API-konzol megnyitása.

3.  Az a **listId** mezőbe írja be a lista azonosítóját. Adja meg az előfizetési kulcs, és válassza ki **küldése**.

  ![API - frissítés Search-Index konzol válasz tartalmú panelen Kifejezéslisták](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Hozzon létre egy kifejezéslista
1.  Nyissa meg a [kifejezés lista felügyeleti API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

  A **sorolja fel a kifejezés - létrehozása** lap megnyitásakor.

2.  A **Open API tesztelési konzollal**, válassza ki a régiót, amelyben leginkább a tartózkodási ismerteti. 

  ![Listák távon – létrehozása lap régió kiválasztása](images/test-drive-region.png)

  A **sorolja fel a kifejezés - létrehozása** API-konzol megnyitása.
 
3.  Az a **Ocp-Apim-Subscription-Key** adja meg az előfizetési kulcs.

4.  Az a **kérelem törzse** mezőben adjon meg értéket a **neve** (például MyList) és **leírás**.

  ![Listák távú - konzol kérelem törzse név és leírás létrehozása](images/try-terms-list-create-1.png)

5.  Kulcs-érték pár helyőrző használata több leíró metaadatok rendelhet hozzá a listához.

        {
           "Name": "MyExclusionList",
           "Description": "MyListDescription",
           "Metadata": 
           {
              "Category": "Competitors",
              "Type": "Exclude"
           }
        }

  Adja hozzá listametaadatok kulcs-érték párok, és nem tényleges feltételeket.
 
6.  Kattintson a **Küldés** gombra. A lista létrejön. Megjegyzés: a **azonosító** érték, amely az új lista társítva van. Ez az azonosító más kifejezés lista felügyeleti funkciók van szükség.

  ![Listák távon – válasz konzol tartalom mezőre a Listaazonosító létrehozása](images/try-terms-list-create-2.png)
 
7.  Feltételek hozzáadása MyList. A bal oldali menü alatt **kifejezés**válassza **kifejezés hozzáadása**. 

  A **távú - kifejezés hozzáadása** lap megnyitásakor. 

8.  A **Open API tesztelési konzollal**, válassza ki a régiót, amelyben leginkább a tartózkodási ismerteti. 

  ![Távon – kifejezés lapon kiválasztott terület hozzáadása](images/test-drive-region.png)

  A **távú - kifejezés hozzáadása** API-konzol megnyitása.
 
9.  Az a **listId** mezőbe írja be a lista azonosítója, ami akkor jön létre, és válassza ki, hogy **nyelvi**. Adja meg az előfizetési kulcs, és válassza ki **küldése**.

  ![Távon – kifejezés konzol lekérdezési paraméterek hozzáadása](images/try-terms-list-create-3.png)
 
10. Győződjön meg arról, hogy a kifejezés hozzáadta-e a listában, a bal oldali menüben válassza a **kifejezés**, majd válassza ki **minden használati első**. 

  A **kifejezés - minden használati első** API-konzol megnyitása.

11. Az a **listId** mezőbe, majd adja meg az előfizetési kulcs és adja meg a lista azonosítója. Kattintson a **Küldés** gombra.

12. Az a **válasz tartalma** ellenőrizze a megadott feltételeket.

  ![Kifejezés - Get minden használati konzol válasz tartalmú listák a feltételeket, amelyeket a megadott](images/try-terms-list-create-4.png)
 
13. Adjon hozzá néhány további feltételeket. Most, hogy létrehozott egy egyéni neveinek listáját, próbálja meg [valamilyen szöveget vizsgálatának](try-text-api.md) egyéni kifejezés használatával. 

## <a name="delete-terms-and-lists"></a>Listák szerződési feltételek törlése

Egy kifejezést vagy egy lista törlése nagyon egyszerű. Az API-t használja a következő feladatokat végezheti el:

- Egy kifejezés törlése. (**Távon – Törlés**)
- A lista összes használati törlése a lista törlése nélkül. (**Távon – minden feltételek törlése**)
- Törölje a listáját, és annak teljes tartalmát. (**Kifejezéslisták - törlési**)

Ebben a példában egyetlen kifejezés törlése.

1.  Az a [kifejezés lista felügyeleti API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), a bal oldali menüben válassza ki a **kifejezés**, majd válassza ki **törlése**. 

  A **távon – Törlés** nyílik meg.

2. A **Open API tesztelési konzollal**, válassza ki a régiót, amelyben leginkább a tartózkodási ismerteti. 

  ![Kifejezés - törlési lap régió kiválasztása](images/test-drive-region.png)

  A **távon – Törlés** API-konzol megnyitása.
  
3.  Az a **listId** mezőbe írja be a lista, amely egy kifejezés, a törölni kívánt azonosítója. Ezt az Azonosítót az a szám (ebben a példában **122**) adja vissza, amely a **kifejezés - Get részletei láthatók** MyList konzolon. Adja meg a kifejezés, és válasszon egy nyelvet.
 
  ![Kifejezés - törlési konzol lekérdezési paraméterek](images/try-terms-list-delete-1.png)

4.  Adja meg az előfizetési kulcs, és válassza ki **küldése**.

5.  Győződjön meg arról, hogy a kifejezés törölve lett, használja a **kifejezés listája – mindezt** konzolon.

  ![Listák távon – minden konzol válasz content mezőre, hogy törölte-e időszak beolvasása](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Módosítsa a lista adatai

A lista nevének és leírásának szerkesztése, és adja hozzá a metaadatokat elemek.

1.  Az a [kifejezés lista felügyeleti API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), a bal oldali menüben válassza ki a **kifejezés sorolja fel**, majd válassza ki **részletek frissítése**. 

  A **kifejezés - frissítés részletei láthatók** lap megnyitásakor.

2. A **Open API tesztelési konzollal**, válassza ki a régiót, amelyben leginkább a tartózkodási ismerteti. 

  ![Kifejezéslisták - frissítés részletei lap régió kiválasztása](images/test-drive-region.png)

  A **kifejezés - frissítés részletei láthatók** API-konzol megnyitása.

3.  Az a **listId** mezőbe, majd adja meg az előfizetési kulcs és adja meg a lista azonosítója.

4.  Az a **kérelem törzse** mezőbe, majd válassza ki és a beküldéshez **küldése**.

  ![Kifejezéslisták – részletek frissítése konzol kérelem törzse módosítások](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>További lépések

A REST API használata a kódban, vagy kezdje a [kifejezés tartalmazza a .NET – rövid útmutató](term-lists-quickstart-dotnet.md) integrálhatja az alkalmazást.
