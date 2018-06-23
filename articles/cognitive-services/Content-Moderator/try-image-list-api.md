---
title: Közepes szintű lemezképek egyéni listák segítségével az Azure Content moderátor |} Microsoft Docs
description: 'Kép: egyéni listák a tartalom moderátor API konzolon test-Drive.'
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 2d714f017be16d978ffbb877a2b7e78e1caf9169
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347167"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Közepes szintű egyéni lemezkép listákkal az API-konzolon

Használja a [lista felügyeleti API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) Azure tartalom moderátor egyéni listák lemezképek létrehozásához. Használja a képek egyéni listájának kép moderálás API-val. A lemezkép moderálás művelet értékeli ki a lemezkép. Ha egyéni listák hoz létre, a művelet is összehasonlítja az egyéni azokat a lemezképeket. Egyéni listák segítségével blokkolhatja vagy engedélyezheti a lemezképet.

> [!NOTE]
> Nincs a jelenlegi maximális műveletszámot **5 kép listák** minden listájával, hogy **legfeljebb 10 000 képek**.
>

A lista API segítségével hajtsa végre a következő feladatokat:

- Lista létrehozása.
- Lemezképek hozzáadása a listához.
- Képernyő-lemezképek elleni listáját a lemezképeket.
- Képek törlése a listáról.
- Listájának törlése.
- Lista adatainak szerkesztése.
- Frissítse az index, hogy a lista módosításainak szerepelnek az új vizsgálat.

## <a name="use-the-api-console"></a>Az API-konzollal
Az API-nak az online konzolon is test-drive, meg kell az Előfizetés-kulcs. Ez a található a **beállítások** lap a **Ocp-Apim-előfizetés-kulcs** mezőbe. További információkért lásd: [áttekintése](overview.md).

## <a name="refresh-search-index"></a>Keresési index frissítése

Egy képlistában módosítása után frissítenie kell a későbbi vizsgálataiba szereplő módosítások indexével. Ez a lépés hasonlít hogyan egy keresőmotor (Ha engedélyezve van), az asztalon vagy egy webes keresőmotor folyamatosan frissíti új fájlok vagy lapok indexét.

1.  Az a [kép lista felügyeleti API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), a bal oldali menüben válassza ki **kép sorolja fel**, majd válassza ki **Search-Index frissítése**.

  A **felsorolja a lemezkép - Search-Index frissítése** lap megnyitásakor.

2. A **nyílt API-t tesztelési konzol**, válassza ki azt a régiót, amely a leginkább leírja a tartózkodási helyét. 
 
    ![Kép listák - frissítési Search-Index lap terület kiválasztása](images/test-drive-region.png)

    A **felsorolja a lemezkép - Search-Index frissítése** API-konzol megnyitása.

3.  Az a **listId** mezőbe írja be a lista azonosítóját. Adja meg az előfizetés-kulcsot, és válassza **küldése**.

  ![Kép listák - frissítési keresési Index konzol válasz tartalom mező](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Kép lista létrehozása

1.  Lépjen a [kép lista felügyeleti API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

  A **sorolja fel a lemezkép - létrehozása** lap megnyitásakor. 

3.  A **nyílt API-t tesztelési konzol**, válassza ki azt a régiót, amely a leginkább leírja a tartózkodási helyét.

    ![Kép listák, – létrehozása lap terület kiválasztása](images/test-drive-region.png)

    A **sorolja fel a lemezkép - létrehozása** API-konzol megnyitása.
 
4.  Az a **Ocp-Apim-előfizetés-kulcs** mezőbe írja be az Előfizetés-kulcs.

5.  Az a **Request body** mezőbe írja be az értékeket **neve** (például MyList) és **leírás**.

  ![Listák rendszerkép - konzol kérelem törzse nevének és leírásának létrehozása](images/try-terms-list-create-1.png)

6.  Kulcs-érték pár helyőrzőket a segítségével rendelhet hozzá több leíró metaadatok a listához.

        {
           "Name": "MyExclusionList",
           "Description": "MyListDescription",
           "Metadata": 
           {
             "Category": "Competitors",
             "Type": "Exclude"
           }
        }

  Adja hozzá lista metaadatok kulcs-érték párok, és nem a tényleges képek.
 
7.  Kattintson a **Küldés** gombra. A lista jön létre. Megjegyzés: a **azonosító** érték, amely az új lista tartozik. Ezt az Azonosítót kell a más kép lista felügyeleti funkcióihoz.

  ![Listák rendszerkép - konzol válasz tartalmának mezőre a lista azonosító létrehozása](images/try-terms-list-create-2.png)
 
8.  Adja hozzá a következő lemezképek MyList. A bal oldali menüben válassza ki a **kép**, majd válassza ki **lemezkép hozzáadása**.

  A **rendszerkép - lemezkép hozzáadása** lap megnyitásakor. 

9. A **nyílt API-t tesztelési konzol**, válassza ki azt a régiót, amely a leginkább leírja a tartózkodási helyét.

  ![Kép – vegye fel a lemezkép lapon terület kiválasztása](images/test-drive-region.png)

  A **rendszerkép - lemezkép hozzáadása** API-konzol megnyitása.
 
10. Az a **listId** mezőben adja meg a lista Azonosítóját, ami akkor jön létre, és írja be a hozzáadni kívánt kép URL-CÍMÉT. Adja meg az előfizetés-kulcsot, és válassza **küldése**.

11. Győződjön meg arról, hogy a kép hozzáadta a listához, a bal oldali menüben válassza a **kép**, majd válassza ki **összes lemezkép-azonosítók beolvasása**.

  A **lemezkép - összes lemezkép-azonosítók beolvasása** API-konzol megnyitása.
  
12. Az a **listId** mezőben adja meg a lista Azonosítóját, és írja be az Előfizetés-kulcs. Kattintson a **Küldés** gombra.

  ![Lemezkép - Get-összes lemezkép-azonosítók konzol megjeleníti a megadott lemezképek tartalom mezőben Válasz](images/try-image-list-create-11.png)
 
10. Néhány további lemezképek hozzáadása. Most, hogy a létrehozott egyéni lemezképek listáját, próbálja [képek kiértékelése](try-image-api.md) az egyéni lemezképet lista használatával. 

## <a name="delete-images-and-lists"></a>Lemezképek és listák törlése

Lemezkép vagy a lista törlése nem egyértelmű. Az API-t a következő feladatok végrehajtására használható:

- Rendszerkép törlése. (**Kép – törlése**)
- Egy listán szereplő összes lemezkép törlése a lista törlése nélkül. (**Kép – törölje az összes lemezképet**)
- Egy listát, és annak teljes tartalmát törli. (**Kép listák - törlési**)

Ebben a példában egyetlen lemezkép törlése:

1. Az a [kép lista felügyeleti API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), a bal oldali menüben válassza ki **kép**, majd válassza ki **törlése**. 

  A **kép – törlése** lap megnyitásakor.

2. A **nyílt API-t tesztelési konzol**, válassza ki azt a régiót, amely a leginkább leírja a tartózkodási helyét. 

  ![Lemezkép - Törlés lap terület kiválasztása](images/test-drive-region.png)
 
  A **kép – törlése** API-konzol megnyitása.
 
3.  Az a **listId** mezőben adja meg a lemezkép törlése a lista Azonosítóját.  A száma, az eredmény abban a **lemezkép - összes lemezkép-azonosítók beolvasása** MyList-konzolon. Ezután írja be a **ImageId** a lemezkép törlése. 

A példánkban a Listaazonosító van **58953**, értéke **ContentSource**. A lemezkép-azonosító **59021**, értéke **ContentIds**.

4.  Adja meg az előfizetés-kulcsot, és válassza **küldése**.

5.  Győződjön meg arról, hogy a kép törölve lett, használja a **lemezkép - összes lemezkép-azonosítók beolvasása** konzol.
 
## <a name="change-list-information"></a>Információ az állapotváltozási listája

A lista nevének és leírásának szerkesztése, és metaadatok elemek hozzáadását.

1.  Az a [kép lista felügyeleti API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), a bal oldali menüben válassza ki **kép sorolja fel**, majd válassza ki **frissítés részletei**. 

  A **felsorolja a lemezkép - frissítés részletei** lap megnyitásakor.

2. A **nyílt API-t tesztelési konzol**, válassza ki azt a régiót, amely a leginkább leírja a tartózkodási helyét.  

    ![Kép listák - frissítés részletei lap terület kiválasztása](images/test-drive-region.png)

    A **felsorolja a lemezkép - frissítés részletei** API-konzol megnyitása.
 
3.  Az a **listId** mezőben adja meg a lista Azonosítóját, és írja be az Előfizetés-kulcs.

4.  Az a **Request body** mezőben, hogy a módosítást kell végrehajtania, és válassza a **küldése** gombra a lapon.

  ![Kép listák - frissítés részletei konzol kérelem törzse módosítások](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>További lépések

A REST API-t használja a kódban, vagy indítsa el a [kép felsorolja a .NET gyors üzembe helyezés](image-lists-quickstart-dotnet.md) integrálása az alkalmazást.
