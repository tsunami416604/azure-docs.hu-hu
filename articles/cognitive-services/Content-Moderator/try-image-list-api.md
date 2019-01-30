---
title: Az egyedi listákkal és az API-konzol - Content Moderator mérsékelt képek
titlesuffix: Azure Content Moderator
description: A lista felügyeleti API-t az Azure Content Moderator, lemezképek egyéni listák létrehozásához használhatja.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 727c12d50ab9233fadc6f87d14da1eaa46e8ed35
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217761"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Az API-konzol az egyéni rendszerkép listákkal mérsékelt

Használja a [lista felügyeleti API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) az Azure Content Moderator egyéni listák rendszerképek létrehozásához. Egyéni rendszerképek listáját használja a kép moderálási API-val. A lemezkép moderálás művelet kiértékeli a rendszerképet. Egyéni listák létrehozása, ha a művelet is összehasonlítja a képek az egyedi listákkal. Egyéni listák segítségével a kép engedélyezni vagy letiltani.

> [!NOTE]
> A maximális korlát **5 képlista**, amelyek egyenként **nem haladhatják meg a 10 000 képet**.
>

A lista felügyeleti API-t használja a következő feladatokat végezheti el:

- Lista létrehozása.
- Lemezképek hozzáadása a listához.
- Képernyőképek egy listában található rendszerképek ellen.
- Törölje a lemezképet a listából.
- Lista törlése.
- Listaadatok szerkesztése.
- Frissítse az indexet, hogy az új beolvasások észleljék a lista módosításait.

## <a name="use-the-api-console"></a>Az API-konzol használata
Az API az online konzolon is próbálhatják ki őket, meg kell az előfizetési kulcs. Ez található a **beállítások** lap a **Ocp-Apim-Subscription-Key** mezőbe. További információkért lásd az [Áttekintést](overview.md).

## <a name="refresh-search-index"></a>Frissítse a search-index

Miután módosít egy képlista, frissítenie kell az index későbbi vizsgálataiba szereplő módosítások. Ebben a lépésben a hasonló hogyan egy keresőmotor, az asztalon (Ha engedélyezve van) és a egy webes keresőmotor folyamatosan frissíti új fájlok és lapok indexét.

1.  A a [kép lista felügyeleti API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), a bal oldali menüben válassza ki a **kép sorolja fel**, majd válassza ki **Search-Index frissítése**.

  A **felsorolja a lemezkép - Search-Index frissítése** lap megnyitásakor.

2. A **Open API tesztelési konzollal**, válassza ki a régiót, amelyben leginkább a tartózkodási ismerteti. 
 
    ![Rendszerképlisták - frissítés Search-Index lapot régió kiválasztása](images/test-drive-region.png)

    A **felsorolja a lemezkép - Search-Index frissítése** API-konzol megnyitása.

3.  Az a **listId** mezőbe írja be a lista azonosítóját. Adja meg az előfizetési kulcs, és válassza ki **küldése**.

  ![Rendszerképlisták - frissítés Search-Index konzol válasz tartalmú panelen](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Kép lista létrehozása

1.  Nyissa meg a [kép lista felügyeleti API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

  A **listázza a rendszerkép - létrehozása** lap megnyitásakor. 

3.  A **Open API tesztelési konzollal**, válassza ki a régiót, amelyben leginkább a tartózkodási ismerteti.

    ![Listák kép – létrehozása lap régió kiválasztása](images/test-drive-region.png)

    A **listázza a rendszerkép - létrehozása** API-konzol megnyitása.
 
4.  Az a **Ocp-Apim-Subscription-Key** adja meg az előfizetési kulcs.

5.  Az a **kérelem törzse** mezőben adjon meg értéket a **neve** (például MyList) és **leírás**.

  ![Listák kép – a konzol kérelem törzse név és leírás létrehozása](images/try-terms-list-create-1.png)

6.  Kulcs-érték pár helyőrző használata több leíró metaadatok rendelhet hozzá a listához.

        {
           "Name": "MyExclusionList",
           "Description": "MyListDescription",
           "Metadata": 
           {
             "Category": "Competitors",
             "Type": "Exclude"
           }
        }

  Listametaadatok kulcs-érték párok, és nem a tényleges lemezképek hozzáadása.
 
7.  Kattintson a **Küldés** gombra. A lista létrejön. Megjegyzés: a **azonosító** érték, amely az új lista társítva van. Egyéb kép lista felügyeleti funkciók ezzel az Azonosítóval kell rendelkeznie.

  ![Listák kép – válasz konzol tartalom mezőre a Listaazonosító létrehozása](images/try-terms-list-create-2.png)
 
8.  Ezután adja hozzá lemezképek MyList. A bal oldali menüben válassza ki a **kép**, majd válassza ki **lemezkép hozzáadása**.

  A **lemezkép - lemezkép hozzáadása** lap megnyitásakor. 

9. A **Open API tesztelési konzollal**, válassza ki a régiót, amelyben leginkább a tartózkodási ismerteti.

  ![Kép – a lemezkép lapon kiválasztott terület hozzáadása](images/test-drive-region.png)

  A **lemezkép - lemezkép hozzáadása** API-konzol megnyitása.
 
10. Az a **listId** mezőbe írja be a lista azonosítója, ami akkor jön létre, és adja meg a hozzáadni kívánt kép URL-CÍMÉT. Adja meg az előfizetési kulcs, és válassza ki **küldése**.

11. Győződjön meg arról, hogy hozzáadta-e a képet a listához, a bal oldali menüben válassza a **kép**, majd válassza ki **összes lemezkép-azonosítók beolvasása**.

  A **kép – az összes lemezkép-azonosítók beolvasása** API-konzol megnyitása.
  
12. Az a **listId** mezőbe, majd adja meg az előfizetési kulcs és adja meg a lista azonosítója. Kattintson a **Küldés** gombra.

  ![Lemezkép - Get-az összes lemezkép-azonosítók konzol megjeleníti a megadott lemezképek tartalom mezőben Válasz](images/try-image-list-create-11.png)
 
10. Adjon hozzá néhány további rendszerképeket. Most, hogy létrehozott egy egyéni rendszerképek listájának, próbálja meg [lemezképek kiértékelése](try-image-api.md) egyéni rendszerkép használatával. 

## <a name="delete-images-and-lists"></a>Képek és listák törlése

Kép vagy lista törlése nagyon egyszerű. Az API-t használhatja a következő feladatokat végezheti el:

- Rendszerkép törlése. (**Kép – Törlés**)
- Egy lista összes rendszerkép törlése a lista törlése nélkül. (**Kép – törli az összes rendszerkép**)
- Egy lista törlése annak teljes tartalmával együtt. (**Rendszerképlisták - törlési**)

Ebben a példában egy lemezkép törlése:

1. A a [kép lista felügyeleti API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), a bal oldali menüben válassza ki a **kép**, majd válassza ki **törlése**. 

  A **kép – Törlés** lap megnyitásakor.

2. A **Open API tesztelési konzollal**, válassza ki a régiót, amelyben leginkább a tartózkodási ismerteti. 

  ![Lemezkép - törlési lap régió kiválasztása](images/test-drive-region.png)
 
  A **kép – Törlés** API-konzol megnyitása.
 
3.  Az a **listId** adja meg a lemezkép törlése a lista azonosítója.  Ez az a számot adja vissza a **kép – az összes lemezkép-azonosítók beolvasása** MyList konzolon. Ezután írja be a **ImageId** a kép törléséhez. 

Ebben a példában a listát csomagazonosítója **58953**, értéke **ContentSource**. A lemezkép-azonosító **59021**, értéke **ContentIds**.

4.  Adja meg az előfizetési kulcs, és válassza ki **küldése**.

5.  Győződjön meg arról, hogy a kép törölve lett, használja a **kép – az összes lemezkép-azonosítók beolvasása** konzolon.
 
## <a name="change-list-information"></a>Módosítsa a lista adatai

A lista nevének és leírásának szerkesztése, és adja hozzá a metaadatokat elemek.

1.  A a [kép lista felügyeleti API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), a bal oldali menüben válassza ki a **kép sorolja fel**, majd válassza ki **részletek frissítése**. 

  A **lemezkép - frissítés részletei láthatók** lap megnyitásakor.

2. A **Open API tesztelési konzollal**, válassza ki a régiót, amelyben leginkább a tartózkodási ismerteti.  

    ![Rendszerképlisták - frissítés részletei lap régió kiválasztása](images/test-drive-region.png)

    A **lemezkép - frissítés részletei láthatók** API-konzol megnyitása.
 
3.  Az a **listId** mezőbe, majd adja meg az előfizetési kulcs és adja meg a lista azonosítója.

4.  Az a **kérelem törzse** mezőt, a beküldéshez, és válassza a **küldése** gombra a lapon.

  ![Rendszerképlisták – részletek frissítése konzol kérelem törzse módosítások](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>További lépések

A REST API használata a kódban, vagy kezdje a [rendszerképet megjeleníti .NET – rövid útmutató](image-lists-quickstart-dotnet.md) integrálhatja az alkalmazást.
