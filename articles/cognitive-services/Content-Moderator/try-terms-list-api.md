---
title: Közepes szintű egyéni kifejezés listákkal Azure tartalom moderátor szöveg |} Microsoft Docs
description: A tartalom moderátor API konzolon egyéni kifejezés listák test-Drive.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 2542e4590781879408aafe8d072eceef157e02c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347162"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Közepes szintű egyéni kifejezés listákkal az API-konzolon

Az alapértelmezett globális listáját Azure tartalom moderátor elegendő-e a tartalom moderálás igényeinek. Azonban szükség lehet a szervezet vonatkozó feltételek képernyőn. Például előfordulhat, hogy kívánt versenytársnak címkenevek további ellenőrzésre. 

Használja a [lista felügyeleti API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) feltételeket, hogy a szöveg moderálás API-t használja az egyéni listák létrehozásához. A **szöveg - képernyő** művelet vizsgálatokat végez a szöveget a profán kifejezéseket, és összehasonlítja szöveg egyéni és megosztott feketelistákon ellen.

> [!NOTE]
> Nincs a jelenlegi maximális műveletszámot **5 kifejezés listája** minden listájával, hogy **legfeljebb 10 000 feltételek**.
>

A lista API segítségével hajtsa végre a következő feladatokat:
- Lista létrehozása.
- Feltételek hozzáadása a listához.
- Képernyőfelvétel a feltételek listáját feltételeit ellen.
- Feltételek törlése a listáról.
- Listájának törlése.
- Lista adatainak szerkesztése.
- Frissítse az index, hogy a lista módosításainak szerepelnek az új vizsgálat.

## <a name="use-the-api-console"></a>Az API-konzollal

Az API-nak az online konzolon is test-drive, meg kell az Előfizetés-kulcs. Ez a kulcs található a **beállítások** lap a **Ocp-Apim-előfizetés-kulcs** mezőbe. További információkért lásd: [áttekintése](overview.md).

## <a name="refresh-search-index"></a>Keresési index frissítése

Kifejezés listájának módosítása után frissítenie kell a későbbi vizsgálataiba szereplő módosítások indexét. Ez a lépés hasonlít hogyan egy keresőmotor (Ha engedélyezve van), az asztalon vagy egy webes keresőmotor folyamatosan frissíti új fájlok vagy lapok indexét.

1.  Az a [kifejezés lista felügyeleti API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), a bal oldali menüben válasszon ki **kifejezés sorolja fel**, majd válassza ki **Search-Index frissítése**. 

  A **kifejezés listája - Search-Index frissítése** lap megnyitásakor.

2. A **nyílt API-t tesztelési konzol**, válassza ki azt a régiót, amely a leginkább leírja a tartózkodási helyét. 

  ![Kifejezés listák - frissítési Search-Index lap terület kiválasztása](images/test-drive-region.png)

  A **kifejezés listája - Search-Index frissítése** API-konzol megnyitása.

3.  Az a **listId** mezőbe írja be a lista azonosítóját. Adja meg az előfizetés-kulcsot, és válassza **küldése**.

  ![API - frissítési keresési Index konzol válasz tartalom mező kifejezés listája](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Egy kifejezés lista létrehozása
1.  Lépjen a [kifejezés lista felügyeleti API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

  A **létrehozása kifejezés listája -** lap megnyitásakor.

2.  A **nyílt API-t tesztelési konzol**, válassza ki azt a régiót, amely a leginkább leírja a tartózkodási helyét. 

  ![Listák távon - terület kiválasztása lap létrehozása](images/test-drive-region.png)

  A **létrehozása kifejezés listája -** API-konzol megnyitása.
 
3.  Az a **Ocp-Apim-előfizetés-kulcs** mezőbe írja be az Előfizetés-kulcs.

4.  Az a **Request body** mezőbe írja be az értékeket **neve** (például MyList) és **leírás**.

  ![Listák távon - konzol kérelem törzse nevének és leírásának létrehozása](images/try-terms-list-create-1.png)

5.  Kulcs-érték pár helyőrzőket a segítségével rendelhet hozzá több leíró metaadatok a listához.

        {
           "Name": "MyExclusionList",
           "Description": "MyListDescription",
           "Metadata": 
           {
              "Category": "Competitors",
              "Type": "Exclude"
           }
        }

  Adja hozzá lista metaadatok kulcs-érték párok, és nem tényleges feltételeket.
 
6.  Kattintson a **Küldés** gombra. A lista jön létre. Megjegyzés: a **azonosító** érték, amely az új lista tartozik. Ezt az Azonosítót kell a más kifejezés lista felügyeleti funkcióihoz.

  ![Listák távon - konzol válasz tartalmának mezőre a lista azonosító létrehozása](images/try-terms-list-create-2.png)
 
7.  Feltételek hozzáadása MyList. A bal oldali menü alatti **kifejezés**, jelölje be **hozzáadása kifejezés**. 

  A **távon - kifejezés hozzáadása** lap megnyitásakor. 

8.  A **nyílt API-t tesztelési konzol**, válassza ki azt a régiót, amely a leginkább leírja a tartózkodási helyét. 

  ![Távon - kifejezés lapon kiválasztott terület hozzáadása](images/test-drive-region.png)

  A **távon - kifejezés hozzáadása** API-konzol megnyitása.
 
9.  Az a **listId** mezőben adja meg a lista Azonosítóját, ami akkor jön létre, és válassza ki az értéket **nyelvi**. Adja meg az előfizetés-kulcsot, és válassza **küldése**.

  ![Távon - kifejezés konzol lekérdezési paraméterek hozzáadása](images/try-terms-list-create-3.png)
 
10. Győződjön meg arról, hogy a kifejezés hozzáadta a listához, a bal oldali menüben válassza a **kifejezés**, majd válassza ki **összes feltételek beolvasása**. 

  A **kifejezés - összes feltételek beolvasása** API-konzol megnyitása.

11. Az a **listId** mezőben adja meg a lista Azonosítóját, és írja be az Előfizetés-kulcs. Kattintson a **Küldés** gombra.

12. Az a **válasz tartalom** ellenőrizze a megadott feltételeket.

  ![Kifejezés - Get összes feltételek konzol tartalom mezőben listák a megadott feltételeket](images/try-terms-list-create-4.png)
 
13. Vegyen fel néhány további feltételeket. Most, hogy létrehozott egy egyéni listáját, próbálja [szöveg keresése](try-text-api.md) az egyéni kifejezés lista használatával. 

## <a name="delete-terms-and-lists"></a>Törli a feltételek és listák

A kifejezés vagy egy lista törlése nem egyértelmű. Az API-t használja a következő feladatok elvégzéséhez:

- Egy kifejezés törlése. (**Távon - törlése**)
- Egy listán szereplő összes feltétel törlése a lista törlése nélkül. (**Távon - törli az összes kifejezést**)
- Egy listát, és annak teljes tartalmát törli. (**Kifejezés listák - törlési**)

Ebben a példában egyetlen kifejezés törli.

1.  Az a [kifejezés lista felügyeleti API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), a bal oldali menüben válassza ki a **kifejezés**, majd válassza ki **törlése**. 

  A **távon - törlése** nyílik meg.

2. A **nyílt API-t tesztelési konzol**, válassza ki azt a régiót, amely a leginkább leírja a tartózkodási helyét. 

  ![Kifejezés - Törlés lap terület kiválasztása](images/test-drive-region.png)

  A **távon - törlése** API-konzol megnyitása.
  
3.  Az a **listId** mezőbe írja be a lista, amely egy kifejezés a törölni kívánt azonosító. Ezt az Azonosítót az a szám (a fenti példában **122**), amely eredmény abban az esetben a **kifejezés listája - részletek beszerzése** MyList-konzolon. Adja meg a kifejezés, és válasszon egy nyelvet.
 
  ![Kifejezés - törlés konzol lekérdezési paraméterek](images/try-terms-list-delete-1.png)

4.  Adja meg az előfizetés-kulcsot, és válassza **küldése**.

5.  Győződjön meg arról, hogy törölte-e a kifejezést, használja a **kifejezés listája - Get összes** konzol.

  ![Listák távon – az összes konzol válasz tartalom mezőre, hogy törölte-e kifejezés lekérdezése](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Információ az állapotváltozási listája

A lista nevének és leírásának szerkesztése, és metaadatok elemek hozzáadását.

1.  Az a [kifejezés lista felügyeleti API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), a bal oldali menüben válasszon ki **kifejezés sorolja fel**, majd válassza ki **frissítés részletei**. 

  A **kifejezés listája - frissítés részletei** lap megnyitásakor.

2. A **nyílt API-t tesztelési konzol**, válassza ki azt a régiót, amely a leginkább leírja a tartózkodási helyét. 

  ![Kifejezés listák - frissítés részletei lap terület kiválasztása](images/test-drive-region.png)

  A **kifejezés listája - frissítés részletei** API-konzol megnyitása.

3.  Az a **listId** mezőben adja meg a lista Azonosítóját, és írja be az Előfizetés-kulcs.

4.  Az a **Request body** mezőbe, majd válassza ki és a Szerkesztés **küldése**.

  ![Kifejezés listák - frissítés részletei konzol kérelem törzse módosítások](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>További lépések

A REST API-t használja a kódban, vagy indítsa el a [kifejezés tartalmazza a .NET gyors üzembe helyezés](term-lists-quickstart-dotnet.md) integrálása az alkalmazást.
