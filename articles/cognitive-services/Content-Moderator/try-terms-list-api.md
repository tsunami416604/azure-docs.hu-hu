---
title: Szöveg moderálása egyéni kifejezéslistákkal – Tartalommoderátor
titleSuffix: Azure Cognitive Services
description: A Listakezelés API-val egyéni névjegyzékeket hozhat létre a Szövegmoderálás API-val használandó kifejezésekről.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 16cfb6c15a4d17ff3fb4f7f41f59f9f80af1e9e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75382123"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Az API-konzol egyéni kifejezéslistáival mérsékelt

Az Azure Content Moderator alapértelmezett globális kifejezéslistája a legtöbb tartalommoderálási igénynek megfelel. Azonban szükség lehet bizonyos szervezetspecifikus kifejezések szűrésére is. Például elképzelhető, hogy meg szeretné címkézni az üzleti vetélytársak neveit további ellenőrzés céljából. 

A [Listakezelés API-val](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) egyéni névjegyzékeket hozhat létre a Szövegmoderálás API-val használandó kifejezésekről. A **Szöveg - Képernyő** művelet megvizsgálja a szöveget káromkodás, és összehasonlítja a szöveget az egyéni és a megosztott tiltólisták.

> [!NOTE]
> A maximális korlát **5 kifejezéslista**, amelyek egyenként **nem haladhatják meg a 10 000 kifejezést**.
>

A Listakezelés API-val a következő feladatokat teheti meg:
- Lista létrehozása.
- Kifejezések hozzáadása egy listához.
- Egy listán szereplő kifejezések szűrése.
- Kifejezések törlése egy listából.
- Lista törlése.
- Listaadatok szerkesztése.
- Frissítse az indexet, hogy az új beolvasások észleljék a lista módosításait.

## <a name="use-the-api-console"></a>Az API-konzol használata

Mielőtt tesztelheti az API-t az online konzolon, szüksége van az előfizetési kulcsra. Ez a kulcs a **Beállítások** lapon, az **Ocp-Apim-Subscription-Key** mezőben található. További információkért lásd az [Áttekintést](overview.md).

## <a name="refresh-search-index"></a>Keresési index frissítése

Miután módosítottegy kifejezéslistát, frissítenie kell az indexét, hogy a módosítások bekerüljenek a későbbi vizsgálatokba. Ez a lépés hasonló ahhoz, ahogyan egy keresőmotor az asztalon (ha engedélyezve van) vagy egy webes keresőmotor folyamatosan frissíti az indexét, hogy új fájlokat vagy oldalakat tartalmazzon.

1. A [Kifejezéslista-kezelés API-hivatkozásában](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)válassza a bal oldali menü **Kifejezéslisták parancsát,** majd a **Keresési tárgymutató frissítése parancsot.** 

   Megnyílik **a Kifejezéslisták – Keresési tárgyindex frissítése** lap.

2. Az **Open API tesztelési konzol,** válassza ki a régiót, amely a legszorosabban leírja a helyét. 

   ![Kifejezéslisták – Keresési tárgymutató oldalterület-kijelölésének frissítése](images/test-drive-region.png)

   Megnyílik **a Kifejezéslisták – Frissítés keresési index** API-konzolja.

3. A **listOló mezőbe** írja be a listaazonosítót. Írja be az előfizetési kulcsot, és válassza a **Küldés lehetőséget.**

   ![Kifejezéslisták API – Keresési index konzolválasz tartalmának frissítése](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Kifejezéslista létrehozása
1. Nyissa meg a [Kifejezéslista-kezelés API-hivatkozását.](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) 

   Megnyílik **a Kifejezéslisták – Létrehozás** lap.

2. Az **Open API tesztelési konzol,** válassza ki a régiót, amely a legszorosabban leírja a helyét. 

   ![Kifejezéslisták – Oldalterület kijelölésének létrehozása](images/test-drive-region.png)

   Megnyílik **a Kifejezéslisták – API-konzol létrehozása.**
 
3. Az **Ocp-Apim-Subscription-Key** mezőbe írja be az előfizetési kulcsot.

4. A **Kérelem törzsmezőbe** írja be a **Név** (például MyList) és a **Leírás**értékét.

   ![Kifejezéslisták – Konzolkérelem törzsének és leírásának létrehozása](images/try-terms-list-create-1.png)

5. A kulcsérték párhelyőrzőivel több leíró metaadatot rendelhet a listához.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
             "Category": "Competitors",
             "Type": "Exclude"
          }
       }

   Listametaadatok hozzáadása kulcsérték-párokként, nem pedig tényleges kifejezésekként.
 
6. Válassza a **Küldés**lehetőséget. Létrejön a lista. Jegyezze fel az új listához társított **azonosítóértéket.** Erre az azonosítóra más kifejezéslista-kezelési funkciókhoz is szüksége van.

   ![Kifejezéslisták – A konzolválasz tartalmának létrehozása mező a listaazonosítót jeleníti meg](images/try-terms-list-create-2.png)
 
7. Kifejezések hozzáadása a MyList listához. A bal oldali menü **Kifejezés területén**válassza a Kifejezés **hozzáadása lehetőséget.** 

   Megnyílik **a Kifejezés - Kifejezés hozzáadása** lap. 

8. Az **Open API tesztelési konzol,** válassza ki a régiót, amely a legszorosabban leírja a helyét. 

   ![Kifejezés - Kifejezéslap kijelölésének hozzáadása](images/test-drive-region.png)

   Megnyílik **a Kifejezés - Kifejezés** hozzáadása API-konzol.
 
9. A **listId** mezőbe írja be a létrehozott listaazonosítót, és válasszon egy értéket a **nyelvhez**. Írja be az előfizetési kulcsot, és válassza a **Küldés lehetőséget.**

   ![Kifejezés - Kifejezés konzollekérdezési paramétereinek hozzáadása](images/try-terms-list-create-3.png)
 
10. Annak ellenőrzéséhez, hogy a kifejezés hozzá lett-e adva a listához, a bal oldali menüben válassza a **Kifejezés**lehetőséget, majd **az Összes kifejezés beszerezése parancsot.** 

    Megnyílik **a Kifejezés – Minden kifejezés beszereznie** API-konzol.

11. A **listId** mezőbe írja be a listaazonosítót, majd írja be az előfizetési kulcsot. Válassza a **Küldés**lehetőséget.

12. A **Válasz tartalom** mezőjében ellenőrizze a megadott kifejezéseket.

    ![Kifejezés – Minden feltétel beszerezni e feltételek konzol válasz tartalmát mező felsorolja a feltételeket, hogy a megadott](images/try-terms-list-create-4.png)
 
13. Adjon hozzá még néhány kifejezést. Most, hogy létrehozott egy egyéni kifejezéslistát, próbáljon meg [néhány szöveget beszkanosára beszkanosan](try-text-api.md) beszkanosára használni az egyéni kifejezéslista használatával. 

## <a name="delete-terms-and-lists"></a>Kifejezések és listák törlése

Egy kifejezés vagy egy lista törlése nagyon egyszerű. Az API-t a következő feladatok elvégzésére használhatja:

- Egy kifejezés törlése. (**Kifejezés - Törlés**)
- Egy listán szereplő összes kifejezés törlése a lista törlése nélkül. (**Kifejezés - Minden kifejezés törlése**)
- Egy lista törlése annak teljes tartalmával együtt. (**Kifejezéslisták - Törlés**)

Ez a példa egyetlen kifejezést töröl.

1. A [Kifejezéslista-kezelés API-hivatkozásában](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)válassza a bal oldali menü **Kifejezés**parancsát, majd a **Törlés parancsot.** 

   Megnyílik **a Kifejezés - Törlés.**

2. Az **Open API tesztelési konzol,** válassza ki a régiót, amely a legszorosabban leírja a helyét. 

   ![Kifejezés – Oldalterület kijelölésének törlése](images/test-drive-region.png)

   Megnyílik **a Kifejezés - API törlése** konzol.
  
3. A **listOló mezőbe** írja be annak a listának az azonosítóját, amelyből törölni szeretne egy kifejezést. Ez az azonosító az a szám (a példánkban **122),** amely a **Kifejezéslisták - Részletek begetése** konzolon jelenik meg a MyList számára. Írja be a kifejezést, és válasszon egy nyelvet.
 
   ![Kifejezés - Konzollekérdezési paraméterek törlése](images/try-terms-list-delete-1.png)

4. Írja be az előfizetési kulcsot, és válassza a **Küldés lehetőséget.**

5. A kifejezés törlésének ellenőrzéséhez használja a **Kifejezéslisták – Az összes beszerezni** konzolt.

   ![Kifejezéslisták – Az összes konzolválasz tartalommezője azt mutatja, hogy a kifejezés törölve van](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Listaadatok módosítása

Módosíthatja a lista nevét és leírását, és metaadat-elemeket adhat hozzá.

1. A [Kifejezéslista-kezelés API-hivatkozásában](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)válassza a bal oldali menü **Kifejezéslisták parancsát,** majd a **Részletek frissítése parancsot.** 

   Megnyílik **a Kifejezéslisták – Részletek frissítése** lap.

2. Az **Open API tesztelési konzol,** válassza ki a régiót, amely a legszorosabban leírja a helyét. 

   ![Kifejezéslisták – Részletek frissítése lapterület-kijelölése](images/test-drive-region.png)

   Megnyílik **a Kifejezéslisták – Update Details** API konzol.

3. A **listId** mezőbe írja be a listaazonosítót, majd írja be az előfizetési kulcsot.

4. A **Kérelem törzsmezőben** tegye a módosításokat, majd válassza a **Küldés**lehetőséget.

   ![Term Lists - Részletek frissítése konzol: Törzsszerkesztések kérése](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>További lépések

Használja a REST API-t a kódban, vagy kezdje a [kifejezéslisták .NET gyorsindítással](term-lists-quickstart-dotnet.md) az alkalmazásba való integráláshoz.
